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
            }
        }
    }
}


// ### ✅ 2. 단계별 실습 과제
//
// - **1단계**
//     - Test 라는 이름의 새 스테이지를 생성한다.
//     - 해당 스테이지는 Build 스테이지 이후에 실행되어야 함.
//     - Test stage 라는 메시지를 출력한다.
//     - 변경 사항을 커밋/푸시한 후 업데이트된 파이프라인 빌드를 실행한다.
//     - 오류가 발생하지 않고 실행 로그에 메시지가 표시되는지 확인한다.
// - **2단계**
//     - 리눅스 명령어를 사용하여 해당 프로젝트의 테스트를 진행한다.
//     - build 폴더 안에 index.html 파일이 있는지를 테스트 하는 명령어를 작성해본다.
//     - Node 프로젝트를 테스트 하는 명령어를 작성해본다.
//     - 변경 사항을 커밋 푸시 한 후 업데이트된 파이프라인 빌드를 실행한다.
//     - 오류가 발생하지 않고 실행 로그에 메시지가 표시되는지 확인한다.