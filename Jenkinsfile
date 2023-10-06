pipeline {
  agent {label 'ec2'}

    stages {
        stage('Maven Build') {
            steps {
                sh 'mvn clean install'
           }
                 // Post building archive Java application

            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.jar'
                }
            }
        
        }
        stage('Maven test'){
            steps {
                sh 'mvn test'
            }
        }
        stage('Build Docker Image') {
            steps {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'password', usernameVariable: 'username')]) {
                sh """ 
                    docker build . -t  javaimage:${GET_COMMIT.TAKE(8)}
                    docker login -u ${username} -p ${password}
                    docker push abdelrhmandevops/javaimage:${GET_COMMIT.TAKE(8)}
                    """
                } 
            }
        }
        stage('deploy the javaApp'){
            steps{
                sh """
                    docker stop javaApp || true && docker rm javaApp || true
                    docker pull abdelrhmandevops/javaimage:${GET_COMMIT.TAKE(8)}
                    docker run --name javaApp -d -p 8081:8081 abdelrhmandevops/javaimage:${GET_COMMIT.TAKE(8)}
                """
            }
        }
    }
}
