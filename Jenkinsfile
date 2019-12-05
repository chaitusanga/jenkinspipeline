pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '18.218.144.13', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.217.110.197', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
    }

    stages{
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

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                    	sh "/bin/ls -latr ${WORKSPACE}/**/target "
                        sh "/usr/bin/scp -v -i /Users/csanga/jenkins-project/keys-ec2/tomcat.pem ${WORKSPACE}/**/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "/usr/bin/scp -v -i /Users/csanga/jenkins-project/keys-ec2/tomcat.pem ${WORKSPACE}/**/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat/webapps"
                    }
                }
            }
        }
    }
}