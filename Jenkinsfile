pipeline {
    agent any
    tools {
        maven 'localMAVEN'
    }
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '34.239.111.235', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '35.171.26.33', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
    }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
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
                        sh "scp -i /home/dan/Documents/cli.pem **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/dan/Documents/cli.pem **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}