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
        anyOf {
          branch 'master'
          branch 'dev'
        }
      }
      steps {
        populateRevision()
        sh "docker build -t docker-registry.market.local/documentation-api:$REVISION ."
      }
    }
    
    stage('Publish') {
      when {
        anyOf {
          branch 'master'
          branch 'dev'
        }
      }
      steps {
        sh "docker push docker-registry.market.local/documentation-api:$REVISION"
      }
    }
    
    stage('Deploy to Test cluster') {
      when {
        branch 'dev'
      }
      steps {
        helmUpgradeInstall('test-cluster', 'test', './helm/values/test.yaml', "${REVISION}", 'documentation-api', './helm/documentation-api')
      }
    }
    
    stage('Deploy to Prod cluster') {
      when {
        branch 'master'
      }
      steps {
        helmUpgradeInstall('prod-cluster', 'prod', './helm/values/prod.yaml', "${REVISION}", 'documentation-api', './helm/documentation-api')
      }
    }
  }
  post {
    failure {
      reportToTelegram()
    }
  }
}
