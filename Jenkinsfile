pipeline {
    agent any

    environment {
        DEPLOY_URL = "http://13.48.78.229:3000/api/application/deploy"
        NODE_ENV = "production"
    }

    stages {

        stage('Checkout SCM') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/Ummekulsum320/job_portal_frontend']]
                ])
            }
        }

        stage('Setup .env') {
            steps {
                script {
                    writeFile file: '.env', text: '''
VITE_CANDIDATES_ENDPOINT=http://13.48.78.229:3000/api/application
                    '''.stripIndent()
                    echo ".env created with VITE_CANDIDATES_ENDPOINT"
                }
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'node -v'
                sh 'npm --version'
                sh 'npm ci'
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
                    echo "Triggering deployment at ${DEPLOY_URL}"
                    // Using curl properly quoted to avoid syntax errors
                    sh """
                        curl -X POST "${DEPLOY_URL}" \
                        -H "Content-Type: application/json" \
                        -d '{"status":"success","triggered_by":"jenkins"}'
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment pipeline completed successfully."
        }
        failure {
            echo "❌ Pipeline failed. Check logs for details."
        }
        always {
            echo "📌 Pipeline run finished at ${new Date()}"
        }
    }
}