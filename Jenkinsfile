pipeline {
    agent any

    environment {
        // Set your kubeconfig path here
        KUBECONFIG = 'C:\\Users\\mandl\\.kube\\config'
        PYTHON_PATH = 'C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Python313\\python.exe'
    }

    stages {

        stage('Checkout SCM') {
            steps {
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
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat "\"${env.PYTHON_PATH}\" -m pytest || echo \"No tests yet\""
                }
            }
        }

        stage('Set Minikube Docker Env') {
            steps {
                powershell """
                    # Point Jenkins to Minikube Docker daemon
                    minikube -p minikube docker-env --shell powershell | Invoke-Expression
                """
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t fastapi-demo:latest .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                powershell """
                    # Make sure kubeconfig is set for Jenkins
                    kubectl config use-context minikube
                    kubectl apply -f k8s-deploy.yaml -n default
                """
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
