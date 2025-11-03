pipeline {
    agent any

    environment {
        APP_NAME = "hello-devops-app"
        CONTAINER_NAME = "hello-devops-container"
        IMAGE_TAG = "v2"
        PORT = "5000"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "📦 Checking out source code..."
                git branch: 'dev', url: 'https://github.com/akashpandit3010/Devops-Lab.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo "📥 Setting up Python environment and installing dependencies..."
                bat '''
                python -m venv venv
                call venv\\Scripts\\activate
                python -m pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo "🧪 Running unit tests with pytest..."
                bat '''
                call venv\\Scripts\\activate
                pytest --maxfail=1 --disable-warnings -q --junitxml=results.xml
                '''
                junit 'results.xml'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🛠️ Building Docker image...'
                bat "docker build -t ${APP_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Run Docker Container') {
            steps {
                echo '🚀 Running Docker container...'
                bat """
                for /F "tokens=*" %%i in ('docker ps -aq -f "name=${CONTAINER_NAME}"') do (
                    docker stop %%i
                    docker rm %%i
                )
                docker run -d -p ${PORT}:${PORT} --name ${CONTAINER_NAME} ${APP_NAME}:${IMAGE_TAG}
                REM Wait for container to start
                ping 127.0.0.1 -n 6 >nul
                """
            }
        }

        stage('Health Check') {
            steps {
                echo '🩺 Checking container health...'
                script {
                    def result = bat(returnStatus: true, script: "curl -s http://localhost:${PORT} >nul")
                    if (result != 0) {
                        error("❌ Health check failed! Flask app not reachable.")
                    } else {
                        echo "✅ Flask app is up and reachable!"
                    }
                }
            }
        }
    }

    post {
        always {
            echo "📋 Showing container logs:"
            bat "docker logs ${CONTAINER_NAME} || echo No logs found"
        }
        success {
            echo "🎉 Build, test, and deployment successful!"
        }
        failure {
            echo "⚠️ Pipeline failed — check above logs for details."
        }
    }
}
