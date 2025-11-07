pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ummekulsum320/job_portal_frontend'
            }
        }

        stage('Setup .env') {
            steps {
                script {
                    writeFile file: '.env', text: 'VITE_CANDIDATES_ENDPOINT=http://127.0.0.1:8000/api/candidates'
                    echo ".env created"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
                sh 'npm install --save-dev vitest'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npx vitest --run'
            }
        }
    }

    post {
        success {
            echo '✅ Frontend build and tests succeeded'
        }
        failure {
            echo '❌ Frontend pipeline failed'
        }
    }
}