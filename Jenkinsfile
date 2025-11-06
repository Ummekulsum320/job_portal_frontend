pipeline {
    agent any

    environment {
        APP_ID = "f8Cq0JwIpIz7gBQ4se_ML"
        DEPLOY_URL = "http://13.48.78.229:3000/api/application/deploy"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup .env') {
            steps {
                sh """
                    echo 'VITE_CANDIDATES_ENDPOINT=http://13.48.78.229:3000/api/candidates' > .env
                    echo ".env created with VITE_CANDIDATES_ENDPOINT"
                """
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'node -v && npm --version'
                // install all project dependencies (including vitest)
                sh 'npm install'
                // ensure vitest is installed locally
                sh 'npm install --save-dev vitest'
            }
        }

        stage('Run tests') {
            steps {
                sh '''
                    if [ -f "./node_modules/.bin/vitest" ]; then
                      echo "✅ Running tests with local vitest..."
                      npx vitest --run
                    else
                      echo "⚠ vitest not found. Skipping tests."
                    fi
                '''
            }
        }

        stage('Trigger Deployment API') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                sh '''
                    echo "🚀 Triggering deployment..."
                    json_payload=$(printf '{"applicationId":"%s"}' "$APP_ID")
                    curl -fS -X POST "$DEPLOY_URL" \
                        -H 'accept: application/json' \
                        -H 'Content-Type: application/json' \
                        --data-binary "$json_payload" \
                        -w "\\nHTTP %{http_code}\\n"
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline succeeded and deployment triggered!"
        }
        failure {
            echo "❌ Pipeline failed. Please check the logs."
        }
    }
}