pipeline {
  agent any
  environment {
    DASTARDLY_TARGET_URL='http://testfire.net/'
    IMAGE_WITH_TAG='public.ecr.aws/portswigger/dastardly:latest'
    JUNIT_TEST_RESULTS_FILE='dastardly-report.xml'
    PDF_REPORT_FILE='dastardly-report.pdf'
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
    stage ("Generate PDF report") {
      steps {
        sh '''
          docker run --rm -v ${WORKSPACE}:${WORKSPACE}:rw \
          -w ${WORKSPACE} \
          portswigger/dastardly-report-generator \
          java -jar dastardly-report-generator.jar \
          ${JUNIT_TEST_RESULTS_FILE} \
          ${PDF_REPORT_FILE}
        '''
        archiveArtifacts artifacts: "${PDF_REPORT_FILE}"
      }
    }
  }
  post {
    always {
      script {
        def dastardlyReport = junit testResults: "${JUNIT_TEST_RESULTS_FILE}"
        def failedTestsCount = dastardlyReport.getFailCount()
        if (failedTestsCount > 0) {
          echo "Ignoring test failures..."
        } else {
          echo "All tests passed."
        }
      }
    }
  }
}
