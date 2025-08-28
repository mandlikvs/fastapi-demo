pipeline {
    agent any

    environment {
        // Point Docker to Minikube
        DOCKER_TLS_VERIFY = "1"
        DOCKER_HOST = "tcp://127.0.0.1:50152"
        DOCKER_CERT_PATH = "C:\\Users\\mandl\\.minikube\\certs"
        MINIKUBE_ACTIVE_DOCKERD = "minikube"

        // Python executable
        PYTHON = "C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Python313\\python.exe"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat "\"${env.PYTHON}\" -m pip install --upgrade pip"
                bat "\"${env.PYTHON}\" -m pip install -r requirements.txt"
            }
        }

        stage('Run Tests') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat "\"${env.PYTHON}\" -m pytest || echo \"No tests yet\""
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir("${env.WORKSPACE}") {
                    bat 'docker build -t fastapi-demo:latest .'
                }
            }
        }

        stage('Deploy to K8s') {
            steps {
                dir("${env.WORKSPACE}") {
                    bat 'kubectl apply -f k8s-deploy.yml'
                    bat 'kubectl rollout status deployment/fastapi-demo'
                }
            }
        }
    }
}
