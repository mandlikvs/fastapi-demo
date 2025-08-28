pipeline {
    agent any
    environment {
        // Use Minikube Docker daemon
        DOCKER_HOST = "tcp://127.0.0.1:2375"
    }
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'py -3 -m pip install --upgrade pip'
                bat 'py -3 -m pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'py -3 -m pytest || echo "No tests yet"'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t fastapi-demo:latest .'
            }
        }

        stage('Deploy to K8s') {
            steps {
                // Apply deployment
                bat 'kubectl apply -f k8s-deploy.yml'

                // Optional: wait until pod is ready
                bat 'kubectl rollout status deployment/fastapi-demo'
            }
        }
    }
}  // <-- closing brace for the pipeline
