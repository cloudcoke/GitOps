pipeline {
  agent any
  stages {
     stage('deploy start') {
      steps {
        slackSend(message: "Deploy ${env.BUILD_NUMBER} Started"
        , color: 'good', tokenCredentialId: 'slack-key')
      }
    }      
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
          find . -name "*.yaml" | xargs -I {} kubectl --kubeconfig=/home/jenkins/agent/kubectl_config/config apply -f {}
          '''
        }
      }
    }
     stage('send diff') {
      steps {
        script {
          def publisher = LastChanges.getLastChangesPublisher "PREVIOUS_REVISION", "SIDE", "LINE", true, true, "", "", "", "", ""
          publisher.publishLastChanges()
          def htmlDiff = publisher.getHtmlDiff()
          writeFile file: "deploy-diff-${env.BUILD_NUMBER}.html", text: htmlDiff
        }
        slackSend(message: """${env.JOB_NAME} #${env.BUILD_NUMBER} End
        (<${env.BUILD_URL}/last-changes|Check Last changed>)"""
        , color: 'good', tokenCredentialId: 'slack-key')             
      }
    }
  }
}