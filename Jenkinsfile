pipeline {
    agent none

    environment {
        NETLIFY_SITE_ID = 'fb7101a2-5333-4bbc-b9f7-b63035e2cf92'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    args "--entrypoint=''"
                }
            }

    environment {
        AWS_S3_BUCKET = 'portfolio-489023881839-ap-northeast-2-an'
    }

            steps {
                    withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        echo "Hello S3!" > index.html
                        aws s3 cp index.html s3://$AWS_S3_BUCKET/test2.txt
                    '''
                }
            }
        }


        stage('Build') {
            agent {
                docker { image 'mcr.microsoft.com/playwright:v1.39.0-jammy' }
            }
            steps {
                sh '''
                echo '트리거 테스트 중..'
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }

        stage('Test') {
            agent {
                docker { image 'mcr.microsoft.com/playwright:v1.39.0-jammy' }
            }
            steps {
                sh '''
                    npm test
                '''
            }
        }

        stage('E2E') {
            agent {
                docker { image 'mcr.microsoft.com/playwright:v1.39.0-jammy' }
            }
            steps {
                sh '''
                npm install serve
                node_modules/.bin/serve -s build & sleep 10
                npx playwright test --reporter=html
            '''
            }
        }

        stage('Deploy staging') {
            agent {
                docker { image 'node:18-bullseye' }
            }
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify deploy --dir=build
                '''
            }
        }


        stage('Approval') {
            agent none
            steps{
                timeout(time: 15, unit: 'MINUTES'){
                    input message: '운영환경에 배포할까요?', ok: '네 배포합니다'
                }
            }
        }


        stage('Deploy prod') {
            agent {
                docker { image 'node:18-bullseye' }
            }
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }

        stage('Prod E2E') {
            agent {
                docker { image 'mcr.microsoft.com/playwright:v1.39.0-jammy' }
            }
            environment {
                CI_ENVIRONMENT_URL = 'https://magenta-sunburst-cda1db.netlify.app'
            }
            steps {
                sh 'npx playwright test --reporter=html'
            }
        }
    }
}
