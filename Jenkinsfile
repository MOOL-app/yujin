pipeline {
    agent any
    environment {
            registry = 'https://hub.docker.com/r/julia2039/springboot'
            imageName = 'https://hub.docker.com/r/julia2039/springboot/julia2039/springboot:1.0'
            dockerImage = ''
    }

    stages {
        stage('Build Spring Boot Project') {
            steps {
                script {
                    // Spring Boot 프로젝트 빌드
                    sh 'chmod +x gradlew'
                    sh './gradlew clean build --warning-mode all'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Docker 이미지 빌드. 만들 이미지명 명시 (username/repository name)
                    docker.build("julia2039/springboot:1.0", "-f Dockerfile .")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Docker 이미지를 Docker Hub로 푸시함
                    docker.withRegistry('https://registry.hub.docker.com', 'julia2039') {
                        docker.image("julia2039/springboot:1.0").push()
                    }
                }
            }
        }

        stage('Stop and Remove Existing Container') {
            steps {
                script {
                    // 기존에 동작 중인 컨테이너 중지 및 삭제함
                    sh 'docker ps -q --filter "name=spring-boot-server" | grep -q . && docker stop spring-boot-server && docker rm spring-boot-server || true'
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Docker 컨테이너 실행
                    sh 'docker run -p 8080:8080 -d --name=spring-boot-server julia2039/springboot:1.0'
                }
            }
        }

        stage('Clean Up Unused Docker Images') {
            steps {
                script {
                    // 태그가 겹친 이미지 삭제
                    sh 'docker rmi -f $(docker images -f "dangling=true" -q) || true'
                }
            }
        }

    }

    post {
        always {
            cleanWs()
        }
    }
}
