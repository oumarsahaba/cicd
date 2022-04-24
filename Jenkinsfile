pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'java11'
    }

    stages {
         stage('Install on nexus') {
            
            steps {
                    echo 'initialise..'
                    sh 'mvn clean install'
                }
        }
        stage('Paralel'){
            parallel{
       
        stage('Test') {
            steps {
                echo 'testing..'
                bat 'mvn test'
            }



       }
        stage('Quality gate') {
            steps {
                echo 'analyse sonar..'
                bat 'mvn sonar:sonar -Dsonar.login=ea0ff3152d936b3f2f760068834bdd7bbc323ebc'
            }
        }
        stage('Check Quality gate') {
            steps {
                echo 'tester si le résultat passe le seuil....'
                waitForQualityGate abortPipeline: true
            }
        }
        stage('Deploy DEV') {
            options {
                timeout(time: 0.2, unit: 'HOURS')
            }
            when {
                branch 'main'
            }
            steps {
                echo 'si le build s\'effectue sur la banche main'
                sh 'mvn deploy'
            }
    }
         stage('Deploy REC') {
                    
                    when {
                        branch 'develop'
                    }
                    steps {
                        echo 'si le build s\'effectue sur la banche release'
                        sh 'mvn deploy'
                    }
            }
         stage('Test deploy dev'){
                    when {
                        branch 'develop'
                    }
                    steps{
                        echo 'tester si le déploiment dans dev s\'est bien passé'
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
                    echo'envoyer notification'
            }
            success{
                    echo 'envoyer notification'
            }
            changed{
                    echo'envoyer notification'
            }

            unstable{
                    echo'envoyer notification'
            }
            failure{
                    echo'envoyer notification'
            }



         }

}
