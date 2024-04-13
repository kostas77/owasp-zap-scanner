pipeline {
    agent any

    parameters {
        choice(
            choices: ['Baseline', 'APIS', 'Full'],
            description: 'Type of scan that is going to perform inside the container',
            name: 'SCAN_TYPE'
        )
        string(
            defaultValue: 'https://medium.com/',
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
                    // Check if the container already exists and is running
//                     def containerRunning = sh(script: 'docker ps -q -f name=owasp', returnStatus: true) == 0
//                     if (!containerRunning) {
                        // Pull the latest image and start the container
                        sh 'docker pull owasp/zap2docker-stable:latest'
                        sh 'docker run --platform linux -dt --name owasp owasp/zap2docker-stable /bin/bash'
                        // Introduce a delay to allow the container to start
                        sleep time: 30, unit: 'SECONDS'
//                     } else {
//                         echo 'OWASP ZAP Docker container already running.'
//                     }
                }
            }
        }

        stage('Preparing the Working Directory') {
            steps {
                script {
                    // Check if the container is running
//                     def containerRunning = sh(script: 'docker ps -q -f name=owasp', returnStatus: true) == 0
//                     if (containerRunning) {
//                         // Create the working directory
//                         try {
                            sh 'docker exec owasp mkdir -p /zap/wrk'
//                         } catch (Exception e) {
//                             echo "Failed to create working directory: ${e.message}"
//                         }
//                     } else {
//                         echo 'OWASP ZAP Docker container is not running.'
//                     }
                }
            }
        }

        stage('Scanning target on OWASP container') {
            steps {
                script {
                    def containerRunning = sh(script: 'docker ps -q -f name=owasp', returnStatus: true) == 0
                    if (containerRunning) {
                        // Perform the OWASP ZAP Baseline scan
                        try {
                            sh 'docker exec owasp zap-baseline.py -t https://medium.com/ -r report.html -I'
                        } catch (Exception e) {
                            echo "Failed to perform OWASP ZAP Baseline scan: ${e.message}"
                        }
                    } else {
                        echo 'OWASP ZAP Docker container is not running.'
                    }
                }
            }
        }

//         stage('Scanning target on owasp container') {
//             when {
//                 expression {
//                     params.GENERATE_REPORT == true
//                 }
//             }
//             steps {
//                 script {
//                     scan_type = "${params.SCAN_TYPE}"
//                     echo "----> scan_type: $scan_type"
//                     target = "${params.TARGET}"
//                     if (scan_type == 'Baseline') {
//                         sh """
//                              docker exec owasp \
//                              zap-baseline.py \
//                              -t $target \
//                              -r report.html \
//                              -I
//                          """
//                     } else if (scan_type == 'APIS') {
//                         sh """
//                              docker exec owasp \
//                              zap-api-scan.py \
//                              -t $target \
//                              -r report.html \
//                              -I
//                          """
//                     } else if (scan_type == 'Full') {
//                         sh """
//                              docker exec owasp \
//                              zap-full-scan.py \
//                              -t $target \
//                              -r report.html \
//                              -I
//                          """
//                     } else {
//                         echo 'Something went wrong...'
//                     }
//                 }
//             }
//         }

        stage('Copy Report to Workspace') {
            steps {
                script {
                    sh '''
                         docker cp owasp:/zap/wrk/report.html ${WORKSPACE}/report.html
                     '''
                }
            }
        }

//         stage('Emailing the Report') {
//             steps {
//                 emailext attachLog: true, body: 'Please find attached the OWASP ZAP scan report.', subject: 'OWASP ZAP Report', to: 'recipient@example.com'
//             }
//         }
    }

    post {
        always {
            script {
                // Check if the container exists
                def containerExists = sh(script: 'docker ps -aq -f name=owasp', returnStatus: true) == 0
                if (containerExists) {
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
