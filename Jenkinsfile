pipeline {

    agent any

    tools{
        maven 'maven-3.8.2'

    }

    triggers{
        pollSCM('* * * * *')
    }

    options{
        timestamps()
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5'))
    }

    stages{

        stage('CheckOutCode') {
            steps {
                git credentialsId: '66549094-58fd-4e9c-b3cf-12803125ed5e', url: 'https://github.com/kalyanjyoo/maven-web-application.git'
            }
        }

        stage('Build') {
            steps {
                sh  "mvn clean package"
            }
        }

        stage('ExecuteSonarQubeReport') {
            steps {
                sh  "mvn clean sonar:sonar"
            }
        }

        stage('UploadArtifactsIntoNexus') {
            steps {
                sh  "mvn clean deploy"
            }
        }
        /*
        stage('DeployAppintoTomcatServer') {
            steps {
                sshagent(['a0d60583-c453-4631-bda0-0a04bf95a401']) {
                    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.235.48.171:/opt/tomcat9/webapps/"
                }
            }
        }
        */
        stage('Build Docker Image') {
            steps {
                sh "docker build -t kalyankotni/image-01:1 ."
            }
        }
        
        stage('Deploy Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub_id', variable: 'dockerhubpwd')]) {
                    sh "docker login -u kalyankotni -p ${dockerhubpwd}"
                }
                sh "docker push kalyankotni/image-01:1"
            }
        }

    }
}
