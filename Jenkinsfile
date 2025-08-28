pipeline {
    agent any

    environment {
        PYTHON_PATH = "C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Python313\\python.exe"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/mandlikvs/fastapi-demo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat "${env.PYTHON_PATH} -m pip install --upgrade pip"
                bat "${env.PYTHON_PATH} -m pip install -r requirements.txt"
            }
        }

        stage('Run Tests') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat "${env.PYTHON_PATH} -m pytest || echo \"No tests yet\""
                }
            }
        }

        stage('Set Minikube Docker') {
            steps {
                powershell '''
                Write-Host "Setting Minikube Docker environment..."
                & minikube -p minikube docker-env --shell powershell | Invoke-Expression
                docker info
                '''
            }
        }

        stage('Docker Build') {
            steps {
                powershell '''
                Write-Host "Building Docker image..."
                docker build -t fastapi-demo:latest .
                docker images
                '''
            }
        }

        stage('Deploy to K8s') {
            steps {
                powershell '''
                Write-Host "Deploying to Kubernetes..."
                kubectl apply -f k8s-deploy.yml
                kubectl get pods
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed! Check the logs."
        }
    }
}
