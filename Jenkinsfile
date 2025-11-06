pipeline {
    agent any
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                ls -la
                echo 'Building...'
                rm -rf node_modules package-lock.json
                node --version
                npm --version
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
                }
            }
            steps {
                sh '''
                    echo "Running end-to-end tests with Playwright..."
                    npm install serves
                    node_modules/.bin/serve  -s build &
                    slepp 10
                    npx playwright test
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
            junit 'test-results/junit.xml'
        }
    }
}