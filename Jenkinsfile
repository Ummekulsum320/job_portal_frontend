pipeline {
    agent any

    environment {
        APP_ID = "YOUR-APPLICATION-ID"
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
                // install all dependencies including vitest
                sh 'npm install'
                // install vitest globally (just in case)
                sh 'npm install -g vitest'
            }
        }

        stage('Run tests') {
            steps {
                // run tests safely
                sh '''
                    if npm test -- --run; then
                      echo "✅ Tests passed"
                    else
                      echo "⚠ Tests failed"
                      exit 1
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