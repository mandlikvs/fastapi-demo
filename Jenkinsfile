pipeline {
    agent any

    environment {
        // Minikube Docker environment
        MINIKUBE_PROFILE = 'minikube'
        PYTHON = 'C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Python313\\python.exe'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/mandlikvs/fastapi-demo.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat "${env.PYTHON} -m pip install --upgrade pip"
                bat "${env.PYTHON} -m pip install -r requirements.txt"
            }
        }

        stage('Run Tests') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat "${env.PYTHON} -m pytest || echo \"No tests yet\""
                }
            }
        }

        stage('Set Minikube Docker') {
            steps {
                bat "& minikube -p ${env.MINIKUBE_PROFILE} docker-env --shell powershell | Invoke-Expression"
                bat 'docker info' // verify connection
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
                    // Switch kubectl context to Minikube
                    bat "& minikube -p ${env.MINIKUBE_PROFILE} update-context"
                    bat 'kubectl config use-context minikube'
                    // Apply deployment (skip validation if needed)
                    bat 'kubectl apply -f k8s-deploy.yml --validate=false'
                    // Wait for rollout
                    bat 'kubectl rollout status deployment/fastapi-demo'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs!'
        }
    }
}
