pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo '📦 Checking out source code...'
                git branch: 'main', url: 'https://github.com/akashpandit3010/Devops-Lab.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🛠️ Building Docker image for SmartCalc...'
                bat 'docker build -t smartcalc-service:latest .'
            }
        }

        stage('Run Docker Container') {
            steps {
                echo '🚀 Running SmartCalc container...'
                bat '''
                REM Stop and remove old container if exists
                for /F "tokens=*" %%i in ('docker ps -aq -f "name=smartcalc-container"') do (
                    docker stop %%i  
                    docker rm %%i
                )

                REM Run new container
                docker run -d -p 5000:5000 --name smartcalc-container smartcalc-service:latest

                REM Wait a few seconds for container to start
                ping 127.0.0.1 -n 6 >nul
                '''
            }
        }

        stage('Health Check') {
            steps {
                echo '🩺 Checking if SmartCalc is reachable...'
                script {
                    // Verify if the web calculator is up
                    def result = bat(returnStatus: true, script: 'curl -s http://localhost:5000 | findstr "Calculator"')
                    if (result != 0) {
                        error("❌ Health check failed! Calculator page not reachable.")
                    } else {
                        echo "✅ SmartCalc web app is up and running on http://localhost:5000"
                    }
                }
            }
        }
    }

    post {
        always {
            echo "📋 Showing container logs:"
            bat 'docker logs smartcalc-container || echo No logs found'
        }
        success {
            echo "🎉 SmartCalc app built and deployed successfully!"
        }
        failure {
            echo "⚠️ Build failed — check the logs above."
        }
    }
}
