pipeline {
    agent any

    environment {
        PYTHON = 'C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Python313\\python.exe'
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
                script {
                    def result = bat(script: "\"${env.PYTHON}\" -m pytest", returnStatus: true)
                    if (result == 5) {
                        echo "No tests found. Skipping test failures."
                    } else if (result != 0) {
                        error "Tests failed with exit code ${result}"
                    }
                }
            }
        }

        stage('Set Minikube Docker Env') {
            steps {
                script {
                    try {
                        powershell """
                        \$envOutput = minikube -p minikube docker-env --shell powershell | Out-String
                        if (\$envOutput -match 'DOCKER_HOST') {
                            Invoke-Expression \$envOutput
                            Write-Host "Minikube Docker environment set!"
                        } else {
                            Write-Warning "Minikube Docker environment not available. Using default Docker."
                        }
                        """
                    } catch (e) {
                        echo "Minikube not available, skipping. Using default Docker."
                    }
                }
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
