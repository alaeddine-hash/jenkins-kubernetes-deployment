pipeline {
  environment {
    dockerImageName = "castroup/react-app"
    registryCredential = 'dockerhub-credentials'
  }

  agent any

  stages {
    stage("Checkout Source") {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/alaeddine-hash/jenkins-kubernetes-deployment.git']]])
      }
    }

    stage("Build image") {
      steps {
        script {
          // Use 'docker.build' step to build the Docker image
          dockerImage = docker.build dockerImageName
        }
      }
    }

    stage("Pushing Image") {
      steps {
        script {
          // Use 'docker.withRegistry' step to push the Docker image
          docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage("Deploying React.js container to Kubernetes") {
      steps {
        script {
          def deploymentResult = sh(script: 'kubectl apply -f deployment.yaml', returnStatus: true)
          if (deploymentResult != 0) {
            error "Failed to apply deployment.yaml. Exit code: ${deploymentResult}"
          }

          def serviceResult = sh(script: 'kubectl apply -f service.yaml', returnStatus: true)
          if (serviceResult != 0) {
            error "Failed to apply service.yaml. Exit code: ${serviceResult}"
          }
        }
      }
    }
  }

  post {
    success {
      echo "Deployment successful! Your application is up and running."
    }
    failure {
      echo "Deployment failed. Please check the logs for details."
    }
  }
}
