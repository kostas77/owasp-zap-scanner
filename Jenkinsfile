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
                    // Check if the container already exists and is running
                    def containerOutput = sh(script: 'docker ps -aq -f name=owasp', returnStdout: true).trim()
                    if (containerOutput) {
                        echo 'OWASP ZAP Docker container already running.'
                    } else {
                       // Pull the latest image and start the container
                        sh 'docker pull ghcr.io/zaproxy/zaproxy:stable'
                        sh 'docker run -dt --name ghcr.io/zaproxy/zaproxy:stable /bin/bash'
                        // Introduce a delay to allow the container to start
                        sleep time: 5, unit: 'SECONDS'
                    }
                }
            }
        }

//         stage('Preparing the Working Directory') {
//             steps {
//                 script {
//                     // Check if the container is running
//                     def containerRunning = sh(script: 'docker ps -aq -f name=owasp', returnStatus: true) == 0
//                     if (containerRunning) {
//                         // Create the working directory
//                         try {
//                             sh 'docker exec owasp mkdir -p /zap/wrk'
//                         } catch (Exception e) {
//                             echo "Failed to create working directory: ${e.message}"
//                         }
//                     } else {
//                         echo 'OWASP ZAP Docker container is not running.'
//                     }
//                 }
//             }
//         }

        stage('Testing docker') {
            steps {
                script {
                     sh 'docker ps -a'
                }
            }
        }

        stage('Scanning target on OWASP container') {
            steps {
                script {
                    sh 'docker run --name zap -v ${PWD}:/zap/wrk/:rw -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t https://www.us.elsevierhealth.com/ -r report.html'
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

//         stage('Copy Report to Workspace') {
//             steps {
//                 script {
//                     sh '''
//                          docker cp owasp:/zap/wrk/report.html ${WORKSPACE}/report.html
//                      '''
//                 }
//             }
//         }

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
