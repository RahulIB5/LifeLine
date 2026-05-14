pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "lifelink-backend:v1"
        FRONTEND_IMAGE = "lifelink-frontend:v1"
        DATABASE_URL = "postgresql://lifelink_user:lifelink_password@lifelink-db:5432/lifelink_db"
    }

    stages {
        stage('Docker Network & DB') {
            steps {
                echo "🌐 Setting up Docker network and database..."
                bat '''
                docker network create lifelink-network 2>nul || echo Network already exists
                docker rm -f lifelink-db 2>nul || echo No existing DB to remove
                docker run -d --name lifelink-db --network lifelink-network -e POSTGRES_USER=lifelink_user -e POSTGRES_PASSWORD=lifelink_password -e POSTGRES_DB=lifelink_db postgres:16-alpine
                
                echo ⏳ Waiting 5 seconds for database to be ready...
                timeout /t 5 /nobreak
                '''
            }
        }

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

                docker run -d -p 8000:8000 --name backend --network lifelink-network ^
                -e DATABASE_URL=%DATABASE_URL% ^
                -e SECRET_KEY=your-super-secret-key-here ^
                -e MODEL_PATH=/app/app/ml/models ^
                %BACKEND_IMAGE%

                docker run -d -p 3000:3000 --name frontend --network lifelink-network %FRONTEND_IMAGE%
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
