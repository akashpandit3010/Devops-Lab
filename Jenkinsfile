pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/akashpandit3010/Devops-Lab.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🛠️ Building Docker image...'
                bat 'docker build -t hello-devops:latest .'
            }
        }

        stage('Run Docker Container') {
            steps {
                echo '🚀 Running Docker container...'
                bat '''
                for /F "tokens=*" %i in ('docker ps -aq -f "name=mystifying_kilby"') do (
                    docker stop %i  
                    docker rm %i
                )
                docker run -d -p 5000:5000 --name mystifying_kilby hello-devops:latest
                REM Wait 5 seconds for container to start
                ping 127.0.0.1 -n 6 >nul
                '''
            }
        }

        stage('Health Check') {
            steps {
                script {
                    echo '🩺 Checking health...'
                    def result = bat(returnStatus: true, script: 'curl -s http://localhost:5000 >nul')
                    if (result != 0) {
                        error("Health check failed! App not reachable.")
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Showing container logs:"
            bat 'docker logs mystifying_kilby || echo No logs found'
        }
    }
}
