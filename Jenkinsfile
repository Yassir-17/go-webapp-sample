pipeline {
    agent any
    tools {
 	   go 'go-1.17'
    }

    environment {
   	    GO111MODULE='on'
   	    DOCKER_IMAGE_NAME = 'yassir17/go-webapp-sample'
   	    DOCKER_HUB_CREDENTIALS = credentials('Docker-hub-cred')
   	    CONTAINER_NAME = 'go-webapp-sample'  // Name for the running container
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

        stage('Run Docker Container') {
            steps {
                script {
                    // Stop and remove the container if it already exists
                    sh """
                    docker ps -a | grep ${CONTAINER_NAME} && docker stop ${CONTAINER_NAME} && docker rm ${CONTAINER_NAME} || true
                    """

                    // Run a new container from the built image
                    sh "docker run -d -p 5000:8000 --name ${CONTAINER_NAME} ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        
        stage('Cleaning up') {
           steps {
                // Optionally, stop and remove the container after the pipeline is done
                sh """
                docker ps -a | grep ${CONTAINER_NAME} && docker stop ${CONTAINER_NAME} && docker rm ${CONTAINER_NAME} || true
                """
                sh 'docker rmi ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}'
           }
        }
    }
}
