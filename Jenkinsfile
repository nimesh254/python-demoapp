pipeline {
    agent any

    environment {
        IMAGE_NAME = "demoapp"
        CONTAINER_NAME = "demoapp-container"
        PORT = "5001"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Pulling latest code..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                sh "docker build -t $IMAGE_NAME -f build/Dockerfile ."
            }
        }

        stage('Stop Old Container') {
            steps {
                echo "Stopping old container..."
                sh "docker stop $CONTAINER_NAME || true"
                sh "docker rm $CONTAINER_NAME || true"
            }
        }

        stage('Run New Container') {
            steps {
                echo "Starting new container..."
                sh "docker run -d -p $PORT:5000 --name $CONTAINER_NAME $IMAGE_NAME"
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Running containers:"
                sh "docker ps"
                sh "curl http://localhost:$PORT || true"
            }
        }
    }

    post {
        success {
            echo "Deployment SUCCESS 🚀"
        }
        failure {
            echo "Deployment FAILED ❌ Check logs"
        }
    }
}
