pipeline {
    agent any

    environment {
        NODE_VERSION = '22.21.0'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ummekulsum320/job_portal_frontend'
            }
        }

        stage('Setup .env') {
            steps {
                script {
                    // Set your frontend API endpoint for local testing
                    writeFile file: '.env', text: 'VITE_CANDIDATES_ENDPOINT=http://13.48.78.229:3000/api/candidates'
                    echo ".env created with VITE_CANDIDATES_ENDPOINT"
                }
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'node -v'
                sh 'npm --version'

                script {
                    if (fileExists('package-lock.json')) {
                        sh 'npm ci'
                    } else {
                        sh 'npm install'
                    }
                }

                // Ensure Vitest is installed locally
                sh 'npm install --save-dev vitest'
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    if (fileExists('./node_modules/.bin/vitest')) {
                        echo "✅ Running tests with local vitest..."
                        sh 'npx vitest --run'
                    } else {
                        error "❌ Vitest not found! Make sure it is installed."
                    }
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Frontend pipeline completed successfully!'
            mail to: 'your_email@example.com',
                 subject: 'Job Portal Frontend - Build Success',
                 body: 'The Jenkins build succeeded 🎉.'
        }

        failure {
            echo '❌ Frontend pipeline failed. Check logs!'
            mail to: 'your_email@example.com',
                 subject: 'Job Portal Frontend - Build Failed ❌',
                 body: 'The Jenkins build failed. Please check logs for details.'
        }

        always {
            echo "📌 Pipeline run finished at ${new Date()}"
        }
    }
}
