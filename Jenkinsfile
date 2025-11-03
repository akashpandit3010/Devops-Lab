pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/akashpandit3010/Devops-Lab.git'
        BRANCH = 'main'
        IMAGE_NAME = 'hello-devops-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_URL}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    bat "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    echo 'Running Docker container...'
                    // Stop and remove old container if exists
                    bat 'docker ps -q --filter "name=hello-devops-container" | findstr . && docker stop hello-devops-container && docker rm hello-devops-container || echo No container to remove'
                    // Run new container
                    bat "docker run -d -p 5000:5000 --name hello-devops-container ${IMAGE_NAME}:latest"
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and container deployment successful!'
        }
        failure {
            echo '❌ Build failed. Check logs for more details.'
        }
    }
}
