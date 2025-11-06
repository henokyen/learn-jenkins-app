pipeline {
    agent any
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root'
                }
            }
            steps {
                sh '''
                ls -la
                echo 'Building...'
                npm ci
                npm run build
                ls -la
                '''
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Testing..."
                    test -f build/index.html && echo "index.html exists" || echo "index.html not found"
                    echo 'Additional verification...'
                    npm run test
                '''
            }
        }
        stage ('end-to-end-test-playwright'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                    args '-u root'
                }
            }
            steps {
                sh '''
                    echo "Running end-to-end tests with Playwright..."
                    npm install serve
                    node_modules/.bin/serve  -s build &
                    sleep 10
                    npx playwright test --reporter = html
                '''
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
    post {
        always {
            junit 'jtest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}