pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t devops-1:latest .'
            }
        }

        stage('Deploy Container') {
            steps {
                bat 'docker rm -f devops-app 2>NUL'
                bat 'docker run -d --name devops-app -p 8090:80 devops-1:latest'
            }
        }
    }
}

