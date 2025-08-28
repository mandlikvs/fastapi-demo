pipeline {
    agent any
    environment {
        DOCKER_HOST = "tcp://127.0.0.1:2375"
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
                bat '%PYTHON% -m pip install --upgrade pip'
                bat '%PYTHON% -m pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat '%PYTHON% -m pytest'
                }
            }
}


        stage('Docker Build') {
            steps {
                bat 'docker build -t fastapi-demo:latest .'
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
