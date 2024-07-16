pipeline {
  agent any
  stages {
    stage('git pull') {
      steps {
        // "https://github.com/cloudcoke/GitOps.git" will replace by sed command before RUN
        git url: 'https://github.com/cloudcoke/GitOps.git', branch: 'main'
      }
    }
    stage('k8s deploy'){
      steps {
        script {
          sh '''
          find . -name "*.yaml" | xargs -I {} kubectl --kubeconfig=/home/docker/.kube/config apply -f {}
          '''
        }
      }
    }    
  }
}