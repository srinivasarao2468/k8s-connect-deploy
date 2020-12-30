pipeline {
  agent any
  environment{
    DOCKER_TAG = getDockerTag()
  }
    stages {
      stage('Build image') {
        dir("./app"){
          steps {
            sh "docker build . -t srinivasarao2468/htmlapp:${DOCKER_TAG}"
          }
        }
      }
      stage('Push DockerHub') {
        steps {
          withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
            sh "docker login -u srinivasarao2468 -p ${dockerHubPwd}"
            sh "docker push srinivasarao2468/htmlapp:${DOCKER_TAG}"
          }
        }
      }
      stage('Deploy App') {
        steps {
          sh "chmod -x changeTag.sh"
          sh "./changeTag.sh ${DOCKER_TAG}"
          withCredentials([kubeconfigFile(credentialsId: 'k8s-deploy', variable: 'KUBECONFIG')]) {
            script{
              try {
                sh "kubectl apply -f ."
              }catch(error){
                sh "kubectl create -f ."
              }
            }
          }
        }
      }
    }
}

def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
