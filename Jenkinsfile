pipeline {
    agent any

    environment{
    FILE_NAME = 'index.html'
    }
    

    stages {
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
    }

    post{
        always {
            junit 'test-results/junit.xml'
        }
    }
}