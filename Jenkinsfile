pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'java11'
    }

    environment {
        PATH = "C:\\WINDOWS\\SYSTEM32"
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "localhost:8081"
        NEXUS_REPOSITORY = "maven-releases"
        NEXUS_CREDENTIAL_ID = "nexuscred"
    }

    


    stages {

        stage('Build') {
            steps {
                echo 'building..'
                bat 'mvn clean package'
            }

       }

       stage('Test') {
            steps {
                echo 'testing..'
                bat 'mvn test'
            }

       }
        
         stage('Install on nexus') {
            
            steps {
                    echo 'initialise..'
                    

                    script {
                    
                       
                        bat "mvn install -DskipTests"
                       nexusArtifactUploader artifacts: [[artifactId: 'tracking', classifier: '', file: 'target/tracking-0.0.1.war', type: 'war']], credentialsId: '', groupId: 'sn.ept.git.seminaire.cicd', nexusUrl: 'localhost:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'http://localhost:8081/repository/maven-releases/', version: '0.0.1'

                }
                }
        }

        
        stage('Paralel'){
            parallel{
       
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                bat 'mvn sonar:sonar -Dsonar.login=ea0ff3152d936b3f2f760068834bdd7bbc323ebc'
            }
            }
        }
        stage('Quality gate') {
            steps {
                echo 'tester si le résultat passe le seuil....'
                waitForQualityGate abortPipeline: true
            }
        }
        stage('Deploy DEV') {
        when {
                        branch 'develop'
                    }
            options {
                timeout(time: 10, unit: 'MINUTES')
            }

            steps {
                echo 'si le build s\'effectue sur la banche main'
                bat 'mvn deploy'
            }
    }
        stage('Test deploy dev'){
                        when {
                            branch 'develop'
                        }
                        steps{
                            echo 'Déploiment sur dev success'
                        }
             }
         stage('Deploy REC') {
                    
                    when {
                        branch 'main'
                    }
                    options {
                                    timeout(time: 10, unit: 'MINUTES')
                                }
                    steps {
                        echo 'si le build s\'effectue sur la banche release'
                        bat 'mvn deploy'
                    }
            }

         stage('Test deploy rec'){
                    when {
                        branch 'release'
                    }
                   steps{
                        echo 'tester si le déploiment dans release s\'est bien passé'
                    }
                  }
    }
        }}
         post {
            always{
                    bat 'mvn clean'
                    emailext   attachLog:true, body: 'Votre pipiline du projet a été lancé', subject: 'Build', to: 'ndiayeoumarsahaba@ept.sn'
            }
            success{
                    emailext   attachLog:true ,body: 'Build success', subject: 'Build', to: 'ndiayeoumarsahaba@ept.sn'
            }
            changed{
                    emailext   attachLog:true, body: 'Build changed', subject: 'Build', to: 'ndiayeoumarsahaba@ept.sn'
            }

            unstable{
                    emailext   attachLog:true, body: 'Unstable build', subject: 'Build', to: 'ndiayeoumarsahaba@ept.sn'
            }
            failure{
                    emailext   attachLog:true, body: 'Build failed', subject: 'Build', to: 'ndiayeoumarsahaba@ept.sn'
            }



         }

}




