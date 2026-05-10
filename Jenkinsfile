pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
            reuseNode true
        }
    }

    environment {
        NETLIFY_SITE_ID = 'fb7101a2-5333-4bbc-b9f7-b63035e2cf92'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                echo '트리거 테스트 중..'
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Test stage'
                sh '''
		            test -f build/index.html
                    npm test
                '''
            }
        }

        stage('E2E') {
            steps {
                sh '''
                npm install serve
                node_modules/.bin/serve -s build & sleep 10
                npx playwright test --reporter=html
            '''
            }
        }

        stage('Deploy staging') {
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "프로젝트 스테이징 베포중.. 사이트아이디 : $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build
                '''
            }
        }



        stage('Deploy prod') {
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "프로젝트 베포중.. 사이트아이디 : $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }

        stage('Prod E2E') {

            environment {
                CI_ENVIRONMENT_URL = 'https://magenta-sunburst-cda1db.netlify.app'
            }


            steps {
                sh '''
                    npx playwright test --reporter=html
                '''
            }
        }


    }

    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
