pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "lifelink-backend:v1"
        FRONTEND_IMAGE = "lifelink-frontend:v1"
    }

    stages {

        stage('Build Backend Image') {
            steps {
                echo "🐳 Building Backend Docker Image..."
                bat 'docker build -f Dockerfile.backend -t %BACKEND_IMAGE% .'
            }
        }

        stage('Build Frontend Image') {
            steps {
                echo "🐳 Building Frontend Docker Image..."
                bat 'docker build -f Dockerfile.frontend -t %FRONTEND_IMAGE% .'
            }
        }

        stage('Run Containers') {
            steps {
                echo "🚀 Starting Containers..."
                bat '''
                docker rm -f backend || exit 0
                docker rm -f frontend || exit 0

                docker run -d -p 8000:8000 --name backend %BACKEND_IMAGE%
                docker run -d -p 3000:3000 --name frontend %FRONTEND_IMAGE%
                '''
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD Pipeline Successful"
        }
        failure {
            echo "❌ CI/CD Pipeline Failed"
        }
    }
}
