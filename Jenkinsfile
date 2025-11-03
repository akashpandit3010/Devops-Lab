pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/akashpandit3010/Devops-Lab.git'
        BRANCH = 'main'
        IMAGE_NAME = 'hello-devops'
        CONTAINER_NAME = 'mystifying_kilby'
        PORT = '5000'
    }

    stages {

        stage('Checkout') {
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

                    // Stop and remove existing container if present
                    bat """
                    for /f "tokens=*" %%i in ('docker ps -aq -f "name=${CONTAINER_NAME}"') do (
                        docker stop %%i
                        docker rm %%i
                    )
                    """

                    // Run new container
                    bat "docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest"

                    // Wait for app to start
                    bat "timeout /t 5 >nul"

                    // Show running containers
                    bat "docker ps"

                    // Show app logs
                    bat "docker logs ${CONTAINER_NAME}"
                }
            }
        }

        stage('Health Check') {
            steps {
                script {
                    echo '🔍 Checking application health...'
                    // Test if app is reachable
                    bat """
                    curl http://localhost:${PORT} || exit /b 1
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully! App running at http://localhost:${PORT}"
        }
        failure {
            echo "❌ Pipeline failed. Showing all container logs:"
            bat "docker ps -a"
            bat "docker logs ${CONTAINER_NAME} || echo No logs found"
        }
    }
}
