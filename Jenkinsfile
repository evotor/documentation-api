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
        sh "docker build -t docker-registry.market.local/documentation-api:$BUILD_TAG ."
      }
    }
    stage('Publish') {
      steps {
        sh "docker push docker-registry.market.local/documentation-api:$BUILD_TAG"
      }
    }
    stage('Deploy') {
      steps {
        helmUpgradeInstall('test-cluster', 'test', './helm/values/test.yaml', "${BUILD_TAG}", 'documentation-api', './helm/documentation-api')
      }
    }
  }
  post {
    failure {
      reportToTelegram()
    }
  }
}
