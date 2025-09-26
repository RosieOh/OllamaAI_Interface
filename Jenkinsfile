pipeline {
    agent any

    environment {
        IMAGE_NAME = 'spring-boot-app'
        CONTAINER_NAME = 'spring-app'
        TIMEZONE = 'Asia/Seoul'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "🔄 Git 저장소에서 코드 가져오는 중..."
                git 'https://github.com/RosieOh/OllamaAI_Interface' // 🔁 여기 본인 Git URL로 수정
            }
        }

        stage('Build JAR') {
            steps {
                echo "🛠 Gradle로 Spring Boot 빌드 중..."
                sh './gradlew clean build -x test'  // test 생략 가능
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🐳 Docker 이미지 빌드 중..."
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Stop Old Container') {
            steps {
                echo "🧹 기존 컨테이너 정리..."
                sh """
                   if docker ps -q -f name=${CONTAINER_NAME}; then
                     docker stop ${CONTAINER_NAME} || true
                     docker rm ${CONTAINER_NAME} || true
                   fi
                """
            }
        }

        stage('Run New Container') {
            steps {
                echo "🚀 새로운 컨테이너 실행..."
                sh """
                    docker run -d --name ${CONTAINER_NAME} \
                      -e TZ=${TIMEZONE} \
                      -p 8080:8080 \
                      ${IMAGE_NAME}
                """
            }
        }
    }

    post {
        failure {
            echo "❌ 빌드 실패!"
        }
        success {
            echo "✅ 배포 성공! http://localhost:8080 에서 확인하세요."
        }
    }
}
