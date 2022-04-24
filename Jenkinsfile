pipeline {
    agent any

    tools {
        maven 'maven'
        jdk 'java'
    }

    stages {
        stage('Paralel'){
            parallel{
        stage('Install on nexus') {
            when{
                branch 'release'
            }
            steps {
                    echo 'initialise..'
                    sh 'mvn clean install'
                }
        }
        stage('Test') {
            steps {
                echo 'testing..'
                sh 'mvn test'
            }



       }
        stage('Quality gate') {
            steps {
                echo 'analyse sonar..'
                sh 'mvn sonar:sonar -Dsonar.login=myAuthenticationToken'
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
                    options {
                        timeout(time: 0.2, unit: 'HOURS')
                    }
                    when {
                        branch 'release'
                    }
                    steps {
                        echo 'si le build s\'effectue sur la banche release'
                        sh 'mvn deploy'
                    }
            }
         stage('Test deploy dev'){
                    when {
                        branch 'dev'
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
                    sh 'mvn clean'
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
