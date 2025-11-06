pipeline {
    agent any

    environment {
        NODE_VERSION = '22.21.0'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Setup .env') {
            steps {
                script {
                    // Create a basic .env file for frontend
                    writeFile file: '.env', text: 'VITE_CANDIDATES_ENDPOINT=VITE_CANDIDATES_ENDPOINT'
                    echo ".env created with VITE_CANDIDATES_ENDPOINT"
                }
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'node -v'
                sh 'npm --version'

                script {
                    // Use npm ci if package-lock.json exists, else npm install
                    if (fileExists('package-lock.json')) {
                        sh 'npm ci'
                    } else {
                        sh 'npm install'
                    }
                }
            }
        }

        stage('Run tests') {
            steps {
                sh 'npm test -- --run'
            }
        }

        stage('Trigger Deployment API') {
            steps {
                script {
                    def deployUrl = ''
                    try {
                        deployUrl = credentials('DEPLOY_URL')
                    } catch(Exception e) {
                        echo "⚠️ Warning: DEPLOY_URL credential not found. Skipping deployment API call."
                    }

                    if (deployUrl) {
                        sh "curl -X POST ${deployUrl}"
                        echo "✅ Deployment API triggered successfully."
                    } else {
                        echo "❌ Deployment API skipped due to missing credential."
                    }
                }
            }
        }
    }

    post {
        success {
            echo "🎉 Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs for details."
        }
        always {
            echo "📌 Pipeline run finished at ${new Date()}"
        }
    }
}
