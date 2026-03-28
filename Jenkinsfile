pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'michaelbilhu2'
        IMAGE_NAME = 'devops-1'
        EC2_HOST = '3.17.151.34'
        EC2_USER = 'ubuntu'
        PEM_KEY = 'C:\\Users\\Jenkins\\Keys\\aws-key.pem'
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
                    bat "echo logging in as %DOCKER_USER%"
                    bat "docker logout"
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

        stage('Deploy to EC2') {
            steps {
                bat """
ssh -o StrictHostKeyChecking=no -i "%PEM_KEY%" %EC2_USER%@%EC2_HOST% "docker pull %DOCKERHUB_USER%/%IMAGE_NAME%:%BUILD_NUMBER% && docker rm -f devops-1 || true && docker run -d --name devops-1 -p 80:80 %DOCKERHUB_USER%/%IMAGE_NAME%:%BUILD_NUMBER%"
"""
            }
        }
    }
}
