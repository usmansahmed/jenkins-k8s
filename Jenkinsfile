pipeline {
  agent any
  environment {
    registry = "localhost:5000/app"
    dockerImage = ""
    Deploy = "false"
  }
  stages {
    stage('Poll SCM') {
      steps {
        cleanWs()
        checkout scm
      }
    }
    stage('Build Image') {
      steps {
        script {
          dockerImage = docker.build("${registry}:${BUILD_NUMBER}", "node-test") 
        }
      }
    }
    stage('Push Image') {
      steps {
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Deploy') {
      when {
        expression { Deploy == "true" }
      }
      steps {
        script {
          sh 'sudo kubectl create -f app-deployment.yaml,app-service.yaml'
          sh 'sudo kubectl get all'
        }
      }
    }
    stage('Update') {
      when {
        expression { Deploy == "false" }
      }
      steps {
        script {
          sh "sudo kubectl set image deployment.apps/app app=${registry}:${BUILD_NUMBER}"
        }
      }
    }
  }
}
