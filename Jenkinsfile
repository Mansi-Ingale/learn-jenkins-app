pipeline{
    agent any

    stages{
        //this ia a comment 
        // stage('Build'){
        //     agent{
        //         docker{  
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps{
        //         sh """
        //             #ls -la
        //             #node --version
        //             #npm --version
        //             npm ci
        //             npm run build
        //         """
        //     }
        // }

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

        
    }

    post{
        always{
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}