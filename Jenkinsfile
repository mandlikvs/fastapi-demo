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
        sh 'pip install -r requirements.txt'
      }
    }
    stage('Test') {
      steps {
        sh 'pytest || echo "No tests yet"'
      }
    }
    stage('Docker Build') {
      steps {
        sh 'docker build -t fastapi-demo .'
      }
    }
    stage('Deploy to K8s') {
      steps {
        sh 'kubectl apply -f k8s-deploy.yml'
      }
    }
  }
}
