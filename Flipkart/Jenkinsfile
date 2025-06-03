pipeline {
    agent any

    environment {
        IMAGE = "codersdiary/flipkart"
        VERSION = "${new Date().format('yyyyMMddHHmmss')}"
        CONTAINER_NAME = "flipkart-container"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE}:${VERSION} Flipkart"
                sh "docker tag ${IMAGE}:${VERSION} ${IMAGE}:latest"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo "$PASS" | docker login -u "$USER" --password-stdin'
                    sh "docker push ${IMAGE}:${VERSION}"
                    sh "docker push ${IMAGE}:latest"
                }
            }
        }

        stage('Redeploy Container') {
            steps {
                script {
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    sh "docker run -d --name ${CONTAINER_NAME} -p 8082:80 ${IMAGE}:${VERSION}"
                }
            }
        }
    }
}
