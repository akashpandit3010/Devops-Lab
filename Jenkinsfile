pipeline {
    agent any

    // Run only when code is pushed/merged to 'dev' branch
    triggers {
        pollSCM('* * * * *') // optional: checks every minute
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'dev', url: 'https://github.com/akashpandit3010/Devops-Lab.git'
            }
        }

        stage('Install Dependencies') {
    steps {
        echo "📦 Installing Python dependencies..."
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
        echo "🧪 Running automated tests..."
        bat '''
        call venv\\Scripts\\activate
        pytest --maxfail=1 --disable-warnings -q --junitxml=results.xml
        '''
        junit 'results.xml'
    }
}
            post {
                always {
                    junit 'pytest-report.xml'   // Publishes test results in Jenkins UI
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '🛠️ Building Docker image...'
                bat 'docker build -t hello-devops-app:v2 .'
            }
        }

        stage('Run Docker Container') {
            steps {
                echo '🚀 Running Docker container...'
                bat '''
                for /F "tokens=*" %%i in ('docker ps -aq -f "name=hello-devops-container"') do (
                    docker stop %%i  
                    docker rm %%i
                )
                docker run -d -p 5000:5000 --name hello-devops-container hello-devops-app:v2
                REM Wait for container to start
                ping 127.0.0.1 -n 6 >nul
                '''
            }
        }

        stage('Health Check') {
            steps {
                echo '🩺 Checking health...'
                script {
                    def result = bat(returnStatus: true, script: 'curl -s http://localhost:5000 >nul')
                    if (result != 0) {
                        error("❌ Health check failed! App not reachable.")
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
            bat 'docker logs hello-devops-container || echo No logs found'
        }
        success {
            echo "🎉 All tests passed and container is healthy!"
        }
        failure {
            echo "⚠️ Build failed — check above logs."
        }
    }
}

