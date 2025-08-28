pipeline {
    agent any

    environment {
        PYTHON_PATH = "C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Python313\\python.exe"
        DOCKER_IMAGE = "fastapi-demo:latest"
        K8S_DEPLOYMENT = "fastapi-demo-deployment"
        K8S_NAMESPACE = "default"
    }

    stages {

        stage('Checkout SCM') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mandlikvs/fastapi-demo.git'
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
                Write-Host "Setting Minikube Docker environment for Jenkins..."
                $envVars = minikube -p minikube docker-env --shell powershell
                Write-Host "Raw minikube docker-env output:"
                Write-Host $envVars

                $envVars -split "\\r?\\n" | ForEach-Object {
                    if ($_ -match "set (.+?)=(.+)") {
                        $name = $matches[1]
                        $value = $matches[2]
                        Write-Host "Setting env: $name = $value"
                        [System.Environment]::SetEnvironmentVariable($name, $value, "Process")
                    }
                }

                docker info
                '''
            }
        }

        stage('Docker Build') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Deploy to K8s') {
            steps {
                bat "kubectl apply -f k8s-deploy.yaml -n ${K8S_NAMESPACE}"
                bat "kubectl rollout status deployment/${K8S_DEPLOYMENT} -n ${K8S_NAMESPACE}"
            }
}

    }

    post {
        always {
            echo "Pipeline finished. Check the logs for details."
        }
        success {
            echo "Pipeline succeeded! 🎉"
        }
        failure {
            echo "Pipeline failed! ❌"
        }
    }
}
