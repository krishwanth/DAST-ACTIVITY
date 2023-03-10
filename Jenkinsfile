pipeline {
  agent any
  environment {
    DASTARDLY_TARGET_URL='http://evil.com/'
    IMAGE_WITH_TAG='public.ecr.aws/portswigger/dastardly:latest'
    JUNIT_TEST_RESULTS_FILE='dastardly-report.xml'
  }
  stages {
    stage ("Docker run Dastardly from Burp Suite Scan") {
      steps {
        cleanWs()
        sh '''
          docker run --rm --user $(id -u) -v ${WORKSPACE}:${WORKSPACE}:rw \
          -e DASTARDLY_TARGET_URL=${DASTARDLY_TARGET_URL} \
          -e DASTARDLY_OUTPUT_FILE=${WORKSPACE}/${JUNIT_TEST_RESULTS_FILE} \
          ${IMAGE_WITH_TAG} \
          dastardly \
          --scope "page" \
          --headers \
          --cookies \
          --report-format "xml" \
          --report-file ${WORKSPACE}/${JUNIT_TEST_RESULTS_FILE} \
          --scan-coverage \
          --scan-dom \
          --scan-parameters \
          --scan-forms \
          --scan-links \
          --scan-csrf \
          --scan-file-inclusions \
          --scan-http \
          --scan-session-management \
          --scan-http-verb \
          --scan-http-header \
          --scan-http-status \
          --scan-content \
          --scan-custom-header "X-My-Custom-Header: myvalue" \
          --timeout 300
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
