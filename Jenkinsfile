pipeline {
    agent any
    
    environment {
        PATH = "/Users/nitish.ugru/.rd/bin/docker:$PATH"
    }

    stages {
        stage('Without docker') {
            steps {
                sh '''
                  echo "Hello from outside container"
                  ls -al
                  touch hello1.txt
                '''
            }
        }
        
        stage('With docker') {
            agent {
                docker {
                    image "node:18-alpine"
                    args "-u root"
                    reuseNode true
                }
            }

            steps {
                echo 'Hello from inside container'
                sh '''
                  ls -al
                  node --version
                  npm --version
                  npm ci
                  npm run build
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image "node:18-alpine"
                    args "-u root"
                    reuseNode true
                }
            }

            steps {
                echo "Testing the package"
                sh '''
                  test -f build/index.html
                  npm test
                '''
            }
        }

        stage('End to End with playwright') {
            agent {
                docker {
                    image "mcr.microsoft.com/playwright:v1.54.0-noble"
                    reuseNode true
                }
            }

            steps {
                echo "Testing the package with playwright"
                sh '''
                  npm install serve
                  node_modules/.bin/serve -s build &
                  sleep 20
                  npx playwright test
                '''
            }
        }
    }

    post {
        always {
            junit 'junit-test-results/junit.xml'
        }
    }
}

