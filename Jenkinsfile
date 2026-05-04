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

        stage('Checkout Code') {
            steps {
                echo "Pulling latest code from Git..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh """
                    docker build -t $DOCKER_HUB/$IMAGE_NAME:$TAG -f build/Dockerfile .
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo "Logging into Docker Hub..."

                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing image to Docker Hub..."
                sh """
                    docker push $DOCKER_HUB/$IMAGE_NAME:$TAG
                """
            }
        }

        stage('Stop Old Container') {
            steps {
                echo "Stopping old container if exists..."
                sh """
                    docker rm -f $CONTAINER_NAME || true
                """
            }
        }

        stage('Run New Container') {
            steps {
                echo "Running new container..."
                sh """
                    docker run -d -p $PORT:5000 --name $CONTAINER_NAME $DOCKER_HUB/$IMAGE_NAME:$TAG
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Checking running containers..."
                sh "docker ps"
            }
        }
    }

    post {
        success {
            echo "CI/CD SUCCESS 🚀 Application deployed successfully"
        }

        failure {
            echo "CI/CD FAILED ❌ Check Jenkins logs"
        }
    }
}
