pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'df828524-2441-486a-8893-9a16e08d5779'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = 'use-east-1'
    }

    stages {

        stage('Deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli:2.15.53'
                    args "--entrypoint=''"
                    reuseNode true
                }
            }

            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version      
                        aws ecs register-task-definition --cli-inout-json file://aws/task-definition.json
                    '''
                }
            }
        }


        stage('Build') {
            agent {
                docker {
                    image 'my-playwright'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }



//        stage('Run Tests') {
//            parallel {
//                stage('Unit Tests') {
//                    agent {
//                        docker {
//                            image 'my-playwright'
//                            reuseNode true
//                        }
//                    }
//                    steps {
//                        sh '''
//                            test -f build/index.html
//                            npm test
//                        '''
//                    }
//                    post {
//                        always {
//                            junit 'jest-results/junit.xml'
//                        }
//                    }
//                }
//
//                stage('E2E Local') {
//                    agent {
//                        docker {
//                            image 'my-playwright'
//                            reuseNode true
//                        }
//                    }
//                    steps {
//                        sh '''
//                            serve -s build &
//                            sleep 5
//                            npx playwright test --reporter=html
//                        '''
//                    }
//                    post {
//                        always {
//                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local', reportTitles: '', useWrapperFileDirectly: true])
//                        }
//                    }
//                }
//            }
//        }
//
//        stage('Deploy & E2E Staging') {
//            agent {
//                docker {
//                    image 'my-playwright'
//                    reuseNode true
//                    // args '-u root:root'
//                }
//            }
//
//            environment {
//                CI_ENVIRONMENT_URL = "STAGING_URL_TO_BE_SET"
//            }
//
//            steps {
//                sh '''
//                    netlify --version
//                    echo "Deploying to staging. Site ID: $NETLIFY_SITE_ID"
//                    netlify status
//                    netlify deploy --dir=build --json > deploy-output.json
//                    CI_ENVIRONMENT_URL=$(jq -r '.deploy_url' deploy-output.json)
//                    npx playwright test --reporter=html
//                    '''
//            }
//            post {
//                always {
//                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright E2E Staging', reportTitles: '', useWrapperFileDirectly: true])
//                }
//            }
//        }
//
////        stage('Approval') {
////            steps {
////                timeout(1) {
////                    input message: 'Ready to deploy?', ok: 'Yes, i am sure!'
////                }
////            }
////        }
//
//        stage('Deploy & E2E Production') {
//            agent {
//                docker {
//                    image 'my-playwright'
//                    reuseNode true
//                    // args '-u root:root'
//                }
//            }
//
//            environment {
//                CI_ENVIRONMENT_URL = 'https://friendly-eclair-d5176f.netlify.app'
//            }
//
//            steps {
//                sh '''
//                    node --version
//                    netlify --version
//                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
//                    netlify status
//                    netlify deploy --dir=build --prod
//                    npx playwright test --reporter=html
//                   '''
//            }
//            post {
//                always {
//                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright E2E Production', reportTitles: '', useWrapperFileDirectly: true])
//                }
//            }
//        }
    }
}