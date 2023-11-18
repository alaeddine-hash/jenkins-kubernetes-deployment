pipeline {
  environment {
    dockerImageName = "castroup/react-app"
    registryCredential = 'dockerhub-credentials'
  }

  agent any

  stages {
    stage("Checkout Source") {
      steps {
        // Use 'checkout' step to fetch the source code
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/alaeddine-hash/jenkins-kubernetes-deployment.git']]])
      }
    }

    stage("Build Image") {
      steps {
        script {
          // Use 'docker.build' step to build the Docker image
          dockerImage = docker.build dockerImageName
        }
      }
    }

    stage("Push Image to Registry") {
  steps {
    script {
      // Use 'docker.withRegistry' step to push the Docker image
      docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
        dockerImage.push("latest")
        echo "Docker Image pushed: ${dockerImage.imageName()}:${dockerImage.imageTag()}"
      }
    }
  }
}



    stage("Deploy to Kubernetes") {
      steps {
        script {
          echo "Applying deployment.yaml..."
          def deploymentResult = sh(script: 'kubectl apply -f deployment.yaml', returnStatus: true)
          if (deploymentResult != 0) {
            error "Failed to apply deployment.yaml. Exit code: ${deploymentResult}"
          }

          echo "Applying service.yaml..."
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
