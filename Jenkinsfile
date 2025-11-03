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

                    // Stop & remove any existing container with same name
                    bat """
                    docker ps -q --filter "name=${CONTAINER_NAME}" | findstr . && (
                        docker stop ${CONTAINER_NAME}
                        docker rm ${CONTAINER_NAME}
                    ) || echo No existing container to remove
                    """

                    // ✅ Run new container
                    bat """
                    echo Starting new container...
                    docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${IMAGE_NAME}:latest
                    """

                    // ✅ Wait a few seconds to let Flask app start
                    bat "timeout /t 5 >nul"

                    // ✅ Check running containers
                    bat "docker ps"

                    // ✅ Show container logs
                    bat "docker logs ${CONTAINER_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully! Visit http://localhost:${PORT} to test the app."
        }
        failure {
            echo "❌ Build failed. Check container logs below (if available):"
            bat "docker ps -a"
        }
    }
}
