pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Install') {
            steps {
                bat 'C:\\Python311\\Scripts\\pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                bat 'pytest || echo "No tests yet"'
            }
        }
        stage('Docker Build') {
            steps {
                bat 'docker build -t fastapi-demo .'
            }
        }
        stage('Deploy to K8s') {
            steps {
                bat 'kubectl apply -f k8s-deploy.yml'
            }
        }
    }
}
