pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '/Users/csanga/Documents/apache-maven-3.6.3/bin/mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        
        stage('Deploy to Staging'){
            steps {
                build job: 'deploy-to-staging-pipeline'
            }
        }

        stage('Deploy to Production'){
            steps {
                timeout(time: 5, unit: 'DAYS') {
                    input message: "Approve for PRODUCTION deployment"
                }

                build job: 'deploy-to-prod-pipeline'
            }

            post {
                success {
                    echo 'Code deployed to production'
                }

                failure {
                    echo 'Deployment failed'
                }
            }
        }
    }
}