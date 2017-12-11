pipeline {
  agent any
  triggers {
    pollSCM '* * * * *'
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10')) 
  }
  stages {
    stage('Build') {
      when { 
        branch 'master' 
      }
      steps {
        sh "docker build -t docker-registry.market.local/documentation-api:$BUILD_TAG ."
      }
    }
    stage('Publish') {
      when { 
        branch 'master' 
      }
      steps {
        sh "docker push docker-registry.market.local/documentation-api:$BUILD_TAG"
      }
    }
    stage('Deploy') {
      when { 
        branch 'master' 
      }
      steps {
        kubeRollout('test', 'deployment', 'documentation-api', "docker-registry.market.local/documentation-api:$BUILD_TAG")
      }
    }
  }
  post { 
    always {
      reportToTelegram()
    }
  }
}
