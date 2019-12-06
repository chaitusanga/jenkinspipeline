pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '18.223.252.43', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '3.17.24.27', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
    }

    stages {
        stage('Build'){
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

        stage ('Deployments') {
            parallel {
                stage ('Deploy to Staging') {
                    steps {
                    	//sh "/usr/bin/ssh -vvv -i /Users/Shared/Jenkins/tomcat-new.pem ec2-user@${params.tomcat_dev} 'df -H'"
                    	sh "/usr/bin/scp -i /Users/Shared/Jenkins/tomcat-new.pem ${WORKSPACE}/**/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat/webapps"
                    	sh "echo 'File copied to Staging - ${params.tomcat_dev} server'"
					}
                }

                stage ("Deploy to Production") {
                    steps {
                        sh "/usr/bin/scp -i /Users/Shared/Jenkins/tomcat-new.pem ${WORKSPACE}/**/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat/webapps"
                    	sh "echo 'File copied to Production - ${params.tomcat_prod} server'"
                    }
                } 

            }
        }
    }
}