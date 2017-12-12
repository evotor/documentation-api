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
        if (env.BRANCH_NAME == 'dev') {
          helmUpgradeInstall('test-cluster', 'test', './helm/values/test.yaml', "${REVISION}", 'documentation-api', './helm/documentation-api')
        } else if (env.BRANCH_NAME == 'master') {
          helmUpgradeInstall('prod-cluster', 'prod', './helm/values/prod.yaml', "${REVISION}", 'documentation-api', './helm/documentation-api')
        }
      }
    }
  }
  post {
    failure {
      reportToTelegram()
    }
  }
}
