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

    stage("Build image") {
      steps {
        // Use 'docker.build' step to build the Docker image
        script {
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
          // Use 'kubectl' step to apply Kubernetes configurations
          sh 'kubectl apply -f deployment.yaml'
          sh 'kubectl apply -f service.yaml'
        }
      }
    }
  }
}
