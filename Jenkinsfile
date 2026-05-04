pipeline {
    agent any

    environment {
        DOCKER_HUB = "nimesh254"
        IMAGE_NAME = "demoapp"
        TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "demoapp-container"
        PORT = "5001"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh "docker build -t $DOCKER_HUB/$IMAGE_NAME:$TAG -f build/Dockerfile ."
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push $DOCKER_HUB/$IMAGE_NAME:$TAG"

                sh "docker tag $DOCKER_HUB/$IMAGE_NAME:$TAG $DOCKER_HUB/$IMAGE_NAME:latest"
                sh "docker push $DOCKER_HUB/$IMAGE_NAME:latest"
            }
        }

        stage('Deploy') {
            steps {
                sh "docker rm -f $CONTAINER_NAME || true"
                sh "docker run -d -p $PORT:5000 --name $CONTAINER_NAME $DOCKER_HUB/$IMAGE_NAME:$TAG"
            }
        }

        stage('Verify') {
            steps {
                sh "docker ps"
            }
        }
    }

    post {
        success {
            echo "CI/CD SUCCESS 🚀"
        }
        failure {
            echo "CI/CD FAILED ❌"
        }
    }
}
