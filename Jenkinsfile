pipeline {
  environment {
    dockerGoAppImageName = "mdsahiloss/microservices-monitoring-go-app"
    dockerGoAppImage = ""
    dockerPythonAppV0ImageName = "mdsahiloss/microservices-monitoring-python-app"
    dockerPythonAppV0Image = ""
    dockerPythonAppV1ImageName = "mdsahiloss/microservices-monitoring-python-app-v1"
    dockerPythonAppV1Image = ""
    dockerPythonAppV2ImageName = "mdsahiloss/microservices-monitoring-python-app-v2"
    dockerPythonAppV2Image = ""
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git 'https://github.com/MdSahil-oss/microservices-monitoring.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerGoAppImage       = docker.build(dockerGoAppImageName, "./go-app/")
          dockerPythonAppV0Image = docker.build(dockerPythonAppV0ImageName, "./python-app/v0/")
          dockerPythonAppV1Image = docker.build(dockerPythonAppV1ImageName, "./python-app/v1/")
          dockerPythonAppV2Image = docker.build(dockerPythonAppV2ImageName, "./python-app/v2/")
        }
      }
    }
    stage('Pushing Image') {
      environment {
          registryCredential = 'DockerhubCredentials'
        }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerGoAppImage.push("latest")
            dockerPythonAppV0Image.push("latest")
            dockerPythonAppV1Image.push("latest")
            dockerPythonAppV2Image.push("latest")
          }
        }
      }
    }
    stage('Deploying Application container to Kubernetes') {
      steps {
            withKubeConfig([
                        clusterName: 'minikube',
                        namespace: 'default',
                        contextName: 'jenkins-minikube',
                        serverUrl:   'https://192.168.49.2:8443',
                        credentialsId: 'k8s-creadentials'
                        ]) {
            sh 'kubectl delete -R -f ./k8s/ && kubectl apply -R -f ./k8s/'
        }
      }
    }
  }
}
