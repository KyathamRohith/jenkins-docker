pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "my-jenkins-app"
        DOCKER_TAG = "latest"
        CONTAINER_NAME = "my-nginx-container"
        EXPOSED_PORT = "8080"
        DOCKER_REPO = "rohith1305/my-jenkins-app"
        DOCKER_CREDENTIALS_ID = "93c470a0-e8fe-425c-8f55-932aae8919d4"  // Jenkins credentials ID
        SERVER_IP = "192.168.203.128"  // Replace with your server IP
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/KyathamRohith/jenkins-docker.git'
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        echo "Logged into Docker Hub successfully"
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_REPO}:${DOCKER_TAG}"
                        sh "docker push ${DOCKER_REPO}:${DOCKER_TAG}"
                    }
                }
            }
        }
        stage('Deploy to Server') {
            steps {
                script {
                    sh """
                        docker pull ${DOCKER_REPO}:${DOCKER_TAG}
                        docker ps -a -q --filter name=${CONTAINER_NAME} | xargs -r docker stop || true
                        docker ps -a -q --filter name=${CONTAINER_NAME} | xargs -r docker rm || true
                        docker run -d -p ${EXPOSED_PORT}:80 --name ${CONTAINER_NAME} ${DOCKER_REPO}:${DOCKER_TAG}
                    """
                }
            }
        }
    }
}
