pipeline {
    agent any
    tools {
        maven 'localMaven'
        jdk 'localJDK'	        
    }
    parameters {
        string(name: 'tomcat_dev', defaultValue: '54.152.27.253', description: 'Staging Server')
        string(name: 'tomcat_prod', defaultValue: '3.80.180.245', description: 'Production Server')
    }
    triggers {
        pollSCM('* * * * *')
    }
    stages{
     stage('Build'){
            steps {
                bat 'mvn clean package'
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
                        bat 'echo The current directory is %CD%'
                        bat "pscp -scp -i ..\\..\\keys\\tomcat-demo.ppk webapp\\target\\webapp.war ec2-user@${params.tomcat_dev}:~/apache-tomcat-8.5.53/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        bat "pscp -scp -i ..\\..\\keys\\tomcat-demo.ppk webapp\\target\\webapp.war ec2-user@${params.tomcat_prod}:~/apache-tomcat-8.5.54/webapps"
                    }
                }
            }
        }
    }
}