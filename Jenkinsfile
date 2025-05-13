pipeline {
    agent any

    environment{
    FILE_NAME = 'index.html'
    }
    

    stages {
        // This is a comment
        /*
        stage('Build') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {

                sh '''
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }
        
        */
        

        stage('Test'){
             agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps{
            sh '''
            echo "Test Stage"
            test -f build/$FILE_NAME
            npm test
            '''
            }
        
        
        }

        stage('E2E'){
             agent{
                docker{
                    //mcr.microsoft.com/playwright:v1.52.0-noble
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }

            steps{
            sh '''
            echo "End To End Test Stage"
            npm install -g serve
            serge -s build
            npx playwright test
            '''
            }
        }
    }

    post{
        always {
            junit 'test-results/junit.xml'
        }
    }
}