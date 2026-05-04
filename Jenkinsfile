pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/playwright:v1.44.1-jammy'
        }
    }

    environment {
        CI = 'true'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing npm dependencies...'
                sh 'npm ci'
            }
        }

        stage('Run Unit Tests') {
            steps {
                echo 'Running Jest unit tests...'
                sh 'npm test'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/test-results/junit.xml'
                }
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building React application...'
                sh 'npm run build'
            }
        }

        stage('Run E2E Tests') {
            steps {
                echo 'Running E2E tests with Playwright...'
                sh 'npx playwright test'
            }
            post {
                always {
                    publishHTML([
                        allowMissing: true,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'playwright-report',
                        reportFiles: 'index.html',
                        reportName: 'Playwright Report'
                    ])
                }
            }
        }
    }

    post {
        always {
            echo 'Archiving build artifacts...'
            archiveArtifacts artifacts: 'build/**/*', fingerprint: true, allowEmptyArchive: true
        }
        success {
            echo 'Build and all tests completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check build logs for details.'
        }
    }
}
