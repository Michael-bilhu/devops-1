
pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'michaelbilhu2'
        IMAGE_NAME = 'devops-1'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKERHUB_USER%/%IMAGE_NAME%:%BUILD_NUMBER% -t %DOCKERHUB_USER%/%IMAGE_NAME%:latest ."
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

        stage('Push Images') {
            steps {
                bat "docker push %DOCKERHUB_USER%/%IMAGE_NAME%:%BUILD_NUMBER%"
                bat "docker push %DOCKERHUB_USER%/%IMAGE_NAME%:latest"
            }
        }

        stage('Deploy Locally') {
            steps {
                bat "docker rm -f devops-1 2>NUL"
                bat "docker run -d --name devops-1 -p 8090:80 %DOCKERHUB_USER%/%IMAGE_NAME%:%BUILD_NUMBER%"
            }
        }
    }
}

