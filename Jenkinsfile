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
                    // Ensuring the container is up and running with the necessary configurations
                    sh """
                       docker run --name owasp -d -p 8090:8090 \
                       -v ${PWD}:/zap/wrk/:rw \
                       --restart unless-stopped \
                       ghcr.io/zaproxy/zaproxy:stable
                    """
                }
            }
        }

    stage('Preparing the Working Directory') {
        steps {
            script {
                // Check if the OWASP ZAP container is running
                def containerRunning = sh(script: 'docker inspect -f {{.State.Running}} owasp', returnStdout: true).trim()
                if (containerRunning == 'true') {
                    // Attempt to create the working directory inside the container
                    try {
                        sh 'docker exec owasp mkdir -p /zap/wrk'
                        echo 'Working directory set up successfully.'
                    } catch (Exception e) {
                        echo "Failed to create working directory: ${e.message}"
                        // Optionally, throw an error to halt the pipeline if critical
                        error "Stopping the pipeline as the working directory setup failed."
                    }
                } else {
                    // Log and possibly handle if the container isn't running
                    echo 'OWASP ZAP Docker container is not running. Attempting to start.'
                    sh """
                       docker run --name owasp -d -p 8090:8090 \
                       -v ${PWD}:/zap/wrk/:rw \
                       --restart unless-stopped \
                       ghcr.io/zaproxy/zaproxy:stable
                    """
                    echo 'Container started. Please re-run the pipeline.'
                    // Optionally, stop the pipeline to let the container fully initialize
                    error "Container was not running and has been started. Please re-run the pipeline."
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
                       docker run --name zap -v ${PWD}:/zap/wrk/:rw -t ghcr.io/zaproxy/zaproxy:stable $scriptName -t $target $reportFlag
                    """
                }
            }
        }

//         stage('Emailing the Report') {
//             steps {
//                 emailext attachLog: true, body: 'Please find attached the OWASP ZAP scan report.', subject: 'OWASP ZAP Report', to: 'recipient@example.com'
//             }
//         }

        stage('Copy Report to Workspace') {
            when {
                expression { return params.GENERATE_REPORT }
            }
            steps {
                script {
                    sh 'docker cp owasp:/zap/wrk/report.html ${WORKSPACE}/report.html'
                }
            }
        }
    }

    post {
        always {
            script {
                // Check if the container exists
                def containerOutput = sh(script: 'docker ps -aq -f name=owasp', returnStdout: true).trim()
                if (containerOutput) {
                    // Stop and remove the container
                    try {
                        sh '''
                             docker stop owasp
                             docker rm owasp
                         '''
                    } catch (Exception e) {
                        echo "Failed to stop and remove container: ${e.message}"
                    }
                } else {
                    echo 'OWASP ZAP Docker container does not exist.'
                }
            }
            cleanWs()
        }
    }
}
