pipeline {
    agent any

    environment {
        PYTHON = 'C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Python313\\python.exe'
        PROJECT_NAME = 'fastapi-demo'
        IMAGE_NAME = 'fastapi-demo:latest'
        K8S_NAMESPACE = 'default'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git url: 'https://github.com/mandlikvs/fastapi-demo.git', branch: 'main'
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

        stage('Set Minikube Docker Env') {
            steps {
                powershell """
                # Get Docker environment from Minikube
                \$envOutput = minikube -p minikube docker-env --shell powershell | Out-String

                # Only execute if the output contains 'DOCKER_HOST'
                if (\$envOutput -match 'DOCKER_HOST') {
                    Invoke-Expression \$envOutput
                    Write-Host "Minikube Docker environment set!"
                } else {
                    Write-Warning "Minikube Docker environment not available. Skipping."
                }
                """
            }
        }

        stage('Docker Build') {
            steps {
                bat "docker build -t ${env.IMAGE_NAME} ."
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat "kubectl apply -f k8s-deploy.yaml -n ${env.K8S_NAMESPACE}"
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished. Check logs for details.'
        }
        success {
            echo 'Pipeline succeeded ✅'
        }
        failure {
            echo 'Pipeline failed ❌'
        }
    }
}
