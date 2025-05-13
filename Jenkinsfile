pipeline {
    agent any

    environment{
        NETLIFY_SITE_ID = 'f5a31ab8-21d9-4bda-bee4-006846c32b13'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        
    }
    

    stages {
        // This is a comment
        
        stage('Build') {
            agent{
                docker{
                    image 'node:18'
                    reuseNode true
                }
            }
            steps {

                sh '''
                echo "Small change"
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }
        
        

        stage('Run Tests'){
            parallel{
                 stage('Unit Test'){
                    agent{
                        docker{
                            image 'node:18'
                            reuseNode true
                }
            }

            steps{
                sh '''
                    echo "Test Stage"
                    test -f build/index.html
                    npm test
                '''
            }
            post{
                always {
                    junit 'jest-results/junit.xml'
                }
            }
        }

        stage('E2E'){
             agent{
                docker{
                    //mcr.microsoft.com/playwright:v1.52.0-noble
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                    // It works but not the best way to do so agrs 'u root:root'
                }
            }

            steps{
                sh '''
                    echo "End To End Test Stage"
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
                '''
            }
            post{
                always {
                    junit 'jest-results/junit.xml'
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
        }

            }
        }
        
        stage('Deploy') {
                agent{
                    docker{
                        image 'node:18'
                        reuseNode true
                }
            }
                steps {

                    sh '''
                        npm install netlify-cli
                        node_modules/.bin/netlify --version
                        echo "Deploying to production. SiteID: $NETLIFY_SITE_ID"
                        node_modules/.bin/netlify status
                        node_modules/.bin/netlify deploy --dir=build --prod
                    '''
            }
        }

        stage('Prod E2E'){
             agent{
                docker{
                    //mcr.microsoft.com/playwright:v1.52.0-noble
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                    // It works but not the best way to do so agrs 'u root:root'
                }
            }

            environment{
                CI_ENVIRONMENT_URL = 'https://stellular-hotteok-11705a.netlify.app'
            }

                steps{
                    sh '''
                        npx playwright test --reporter=html
                    '''
                }
                post{
                    always {
                        junit 'jest-results/junit.xml'
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright E2E Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
            }
       
    }


}