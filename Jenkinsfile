pipeline {
    agent any
    tools {
        maven 'localMaven'
        jdk 'localJDK'	        
    }
    parameters {
        string(name: 'tomcat_dev', defaultValue: '54.152.27.253', description: 'Staging Server')
        string(name: 'tomcat_prod', defaultValue: '3.86.144.115', description: 'Production Server')
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
                        bat 'more keys/tomcat-demo.ppk'
                        bat "pscp -scp -i keys/tomcat-demo.ppk webapp/target/webapp.war ec2-user@${params.tomcat_dev}.compute-1.amazonaws.com:~/var/lib/tomcat/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        bat "winscp -i keys/tomcat-demo.ppk webapp/target/webapp.war ec2-user@${params.tomcat_prod}.compute-1.amazonaws.com:~/var/lib/tomcat/webapps"
                    }
                }
            }
        }
    }
}