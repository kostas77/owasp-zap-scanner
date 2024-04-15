pipeline {
    agent any

    parameters {
        choice(
            choices: ['Baseline', 'APIS', 'Full'],
            description: 'Type of scan that is going to perform inside the container',
            name: 'SCAN_TYPE'
        )
        string(
            defaultValue: 'https://www.us.elsevierhealth.com/',
            description: 'Target URL to scan',
            name: 'TARGET'
        )
        booleanParam(
            defaultValue: true,
            description: 'Parameter to know if you want to generate a report.',
            name: 'GENERATE_REPORT'
        )
    }

    stages {
        stage('Setting up OWASP ZAP Docker container') {
            steps {
                script {
                    // Pulling the latest stable image of OWASP ZAP
                    sh 'docker pull ghcr.io/zaproxy/zaproxy:stable'
                }
            }
        }

        stage('Scanning target on OWASP container') {
            steps {
                script {
                    target = "${params.TARGET}"
                    reportFlag = params.GENERATE_REPORT ? "-r report.html" : ""
                    scriptName = (params.SCAN_TYPE == 'Baseline' ? "zap-baseline.py" :
                                 (params.SCAN_TYPE == 'APIS' ? "zap-api-scan.py" : "zap-full-scan.py"))

                    sh """
                       docker run -v ${PWD}:/zap/wrk/:rw -t ghcr.io/zaproxy/zaproxy:stable $scriptName -t $target $reportFlag
                    """
                }
            }
        }

//         stage('Emailing the Report') {
//             steps {
//                 emailext attachLog: true, body: 'Please find attached the OWASP ZAP scan report.', subject: 'OWASP ZAP Report', to: 'recipient@example.com'
//             }
//         }

//         stage('Copy Report to Workspace') {
//             when {
//                 expression { return params.GENERATE_REPORT }
//             }
//             steps {
//                 script {
//                     sh 'docker cp owasp:/zap/wrk/report.html ${WORKSPACE}/report.html'
//                 }
//             }
//         }
    }

//     post {
//         always {
//             script {
//                 // Check if the container exists
//                 def containerOutput = sh(script: 'docker ps -aq', returnStdout: true).trim()
//                 if (containerOutput) {
//                     // Stop and remove the container
//                     try {
//                         sh '''
//                              docker stop containerOutput
//                              docker rm containerOutput
//                          '''
//                     } catch (Exception e) {
//                         echo "Failed to stop and remove container: ${e.message}"
//                     }
//                 } else {
//                     echo 'OWASP ZAP Docker container does not exist.'
//                 }
//             }
//             cleanWs()
//         }
//     }
}
