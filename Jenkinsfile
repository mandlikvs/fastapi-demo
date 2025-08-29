pipeline {
    agent any

    environment {
        // Ensure Jenkins can find kubeconfig
        KUBECONFIG = "C:\\ProgramData\\Jenkins\\.kube\\config"
        // Use Docker with Minikube (adjust if needed)
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
                // Use python.exe directly instead of "py"
                bat '"C:\\Python313\\python.exe" -m pip install --upgrade pip'
                bat '"C:\\Python313\\python.exe" -m pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                bat '"C:\\Python313\\python.exe" -m pytest || echo "No tests yet"'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t fastapi-demo:latest .'
            }
        }

        stage('K8s Check') {
            steps {
                bat 'kubectl get nodes'
                bat 'kubectl get pods -A'
            }
        }

        stage('Deploy to K8s') {
            steps {
                bat 'kubectl apply -f k8s-deploy.yml'
                bat 'kubectl rollout status deployment/fastapi-demo'
            }
        }
    }
}
