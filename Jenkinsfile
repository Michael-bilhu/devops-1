pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'michaelbilhu2'
        IMAGE_NAME = 'devops-1'
        VERSIONED_IMAGE = "${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}
        LATEST_IMAGE = "${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %VERSIONED_IMAGE% -T %LATEST_IMAGE% ."
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                bat "docker push %VERSIONED_IMAGE%"
                bat "docker push %LATEST_IMAGE%"
            }
        }

        stage('Deploy Locally') { steps {
                bat "docker rm -f devops-1 2>NUL"
                bat "docker pull %VERSIONED_IMAGE%"
                bat "docker run -d --name devops-1 -p 8090:80 %VERSIONED_IMAGE%"
            }
        }
    }
}
