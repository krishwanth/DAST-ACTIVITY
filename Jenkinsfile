pipeline {
  agent any
  environment {
    DASTARDLY_TARGET_URL='http://evil.com/'
    IMAGE_WITH_TAG='public.ecr.aws/portswigger/dastardly:latest'
    JUNIT_TEST_RESULTS_FILE='dastardly-report.xml'
  }
  stages {
    stage ("Docker Pull Dastardly from Burp Suite container image") {
      steps {
        sh 'docker pull ${IMAGE_WITH_TAG}'
      }
    }
    stage ("Docker run Dastardly from Burp Suite Scan") {
      steps {
        cleanWs()
        sh '''
          docker run --rm --user $(id -u) -v ${WORKSPACE}:${WORKSPACE}:rw \
          -e DASTARDLY_TARGET_URL=${DASTARDLY_TARGET_URL} \
          -e DASTARDLY_OUTPUT_FILE=${WORKSPACE}/${JUNIT_TEST_RESULTS_FILE} \
          ${IMAGE_WITH_TAG}
        '''
      }
    }
  }
  post {
    always {
      junit testResults: "${JUNIT_TEST_RESULTS_FILE}", skipPublishingChecks: true
    }
  }
}
