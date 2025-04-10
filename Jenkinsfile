pipeline {
  agent any

  environment {
    IMAGE_NAME = 'kirthiksubbiah/java-microservice'
  }

  stages {
    stage('Build and Test') {
      steps {
        sh 'mvn clean install'
      }
    }

    stage('Docker Build & Push') {
      when {
        branch 'develop'
      }
      steps {
        script {
          dockerImage = docker.build("${IMAGE_NAME}:${env.BUILD_NUMBER}")
          docker.withRegistry('https://index.docker.io/v1/', 'Docker-hub-credentials') {
            dockerImage.push()
            dockerImage.push('latest')
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      when {
        branch 'develop'
      }
      steps {
        sh 'kubectl apply -f k8s/deployment.yaml'
        sh 'kubectl apply -f k8s/service.yaml'
      }
    }
  }
}
