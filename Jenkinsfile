pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'michaelbilhu2'
        IMAGE_NAME = 'devops-1'
        IMAGE = "${DOCKERHUB_USER}/${IMAGE_NAME}"
        CONTAINER_NAME = 'devops-app'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE}:${BUILD_NUMBER} ."
                sh "docker tag ${IMAGE}:${BUILD_NUMBER} ${IMAGE}:latest"
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push ${IMAGE}:${BUILD_NUMBER}"
                sh "docker push ${IMAGE}:latest"
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker pull ${IMAGE}:latest
                docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE}:latest
                '''
            }
        }
    }
}
