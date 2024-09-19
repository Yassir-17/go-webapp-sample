pipeline {
    agent any
    tools {
 	   go 'go-1.17'
    }

    environment {
   	    //GO111MODULE='on'
   	    DOCKER_IMAGE_NAME = 'yassir17/go-webapp-sample'
   	    DOCKER_HUB_CREDENTIALS = credentials('Docker-hub-cred')
    }

    stages {
   	    stage('Test') {
           steps {
              git 'https://github.com/Yassir-17/go-webapp-sample.git'
              sh 'go test ./...'
           }
        }    
        stage('Build docker image') {
           steps {
             sh 'docker build -t ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} .'
             sh 'docker images'
          }
        }

        stage('Login to Docker Hub') {
            steps {
              script {
                 // Log in to Docker Hub
                 sh "echo ${DOCKER_HUB_CREDENTIALS_PSW} | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin"
              }
            }
         }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    sh "docker push ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        
        stage('Cleaning up') {
           steps {
                sh 'docker rmi ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}'
           }
        }
    }
}
