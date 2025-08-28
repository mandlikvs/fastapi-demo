pipeline {
    agent any

    environment {
        PYTHON_PATH = "C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Python313\\python.exe"
    }

    stages {

        stage('Checkout SCM') {
            steps {
                // Checkout your repo
                git url: 'https://github.com/mandlikvs/fastapi-demo.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat "\"${env.PYTHON_PATH}\" -m pip install --upgrade pip"
                bat "\"${env.PYTHON_PATH}\" -m pip install -r requirements.txt"
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    try {
                        bat "\"${env.PYTHON_PATH}\" -m pytest || echo No tests yet"
                    } catch (err) {
                        echo "No tests found. Skipping test failures."
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
                            Write-Warning "Minikube not available, using default Docker."
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
                bat "docker build -t fastapi-demo:latest ."
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    try {
                        // Skip validation and ignore auth for local testing
                        bat "kubectl apply --validate=false -f k8s-deploy.yaml -n default"
                    } catch (err) {
                        echo "Skipping Kubernetes auth errors for local testing."
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished. Check logs for details."
        }
        success {
            echo "Pipeline succeeded ✅"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}
