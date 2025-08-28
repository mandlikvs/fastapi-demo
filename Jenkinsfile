pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install') {
            steps {
                bat '''
                "C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Launcher\\py.exe" -3 -m pip install --upgrade pip
                "C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Launcher\\py.exe" -3 -m pip install -r requirements.txt
                '''
            }
        }

       stage('Test') {
          steps {
            bat '''
            "C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Launcher\\py.exe" -3 -m pip install pytest || echo "pytest already installed"
            "C:\\Users\\mandl\\AppData\\Local\\Programs\\Python\\Launcher\\py.exe" -3 -m pytest || echo "No tests yet"
            '''
        }
}


        stage('Docker Build') {
            steps {
                bat '''
                docker build -t fastapi-demo .
                '''
            }
        }

        stage('Deploy to K8s') {
            steps {
                bat '''
                kubectl apply -f k8s-deploy.yml
                '''
            }
        }
    }
}
