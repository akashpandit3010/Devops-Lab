pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/akashpandit3010/Devops-Lab.git'
        BRANCH = 'main'
        IMAGE_NAME = 'hello-devops-app'
        CONTAINER_NAME = 'hello-devops-container'
        PORT = '5000'
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
                    echo '🛠️ Building Docker image...'
                    bat "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    echo '🚀 Running Docker container...'

                    // Stop and remove existing container
                    bat """
                    docker ps -q --filter "name=${CONTAINER_NAME}" | findstr . && (
                        docker stop ${CONTAINER_NAME}
                        docker rm ${CONTAINER_NAME}
                    ) || echo No existing container to remove
                    """

                    // ✅ Run new container
                    bat """
                    docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest
                    """

                    // Wait for app to start
                    bat "timeout /t 5 >nul"

                    // Verify running container
                    bat "docker ps"

                    // Show logs
                    bat "docker logs ${CONTAINER_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully! Visit http://localhost:${PORT}"
        }
        failure {
            echo "❌ Build failed. Check container logs below (if available):"
            bat "docker ps -a"
        }
    }
}
