pipeline{
    agent any

    environment{
        NETLIFY_SITE_ID = '46a1e36e-2e22-4006-a5cc-0d964ba7a841'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages{
        //this ia a comment 
        stage('Build'){
            agent{
                docker{  
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh """
                    ls -la
                    #node --version
                    #npm --version
                    npm ci
                    npm run build
                """
            }
        }

        stage("Test"){
            parallel{
                stage("unit Test"){
                    agent{
                        docker{
                             image 'node:18-alpine'
                             reuseNode true
                        }
                    }


                steps{
                    sh """
                        npm test
                        test -f build/index.html
                    """
                }
                }

                

                stage("E2E"){
                    agent{
                        docker{
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }

                    steps{
                        sh """
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        """
                    }
                }

            }
        }


        stage("Deploy"){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps{
                sh"""
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "deploying to production site ID : $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod

                """
            }
        }

        
    }

    post{
        always{
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}