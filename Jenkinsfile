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
      steps {
        populateRevision()
        sh "docker build -t docker-registry.market.local/documentation-api:$REVISION ."
      }
    }
    stage('Publish') {
      steps {
        sh "docker push docker-registry.market.local/documentation-api:$REVISION"
      }
    }
    stage('Deploy') {
      steps {
        helmUpgradeInstall('test-cluster', 'test', './helm/values/test.yaml', "${REVISION}", 'documentation-api', './helm/documentation-api')
      }
    }
  }
  post {
    failure {
      reportToTelegram()
    }
  }
}
