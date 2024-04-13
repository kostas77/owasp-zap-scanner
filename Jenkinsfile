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
                sh 'docker pull owasp/zap2docker-stable:latest'
                sh 'docker run -dt --name owasp owasp/zap2docker-stable /bin/bash'
            }
        }

        stage('Preparing the Working Directory') {
            when {
                expression {
                    params.GENERATE_REPORT == true
                }
            }
            steps {
                sh 'docker exec owasp mkdir /zap/wrk'
            }
        }

        stage('Scanning target on owasp container') {
            steps {
                script {
                    scan_type = "${params.SCAN_TYPE}"
                    echo "----> scan_type: $scan_type"
                    target = "${params.TARGET}"
                    if (scan_type == 'Baseline') {
                        sh """
                             docker exec owasp \
                             zap-baseline.py \
                             -t $target \
                             -r report.html \
                             -I
                         """
                    } else if (scan_type == 'APIS') {
                        sh """
                             docker exec owasp \
                             zap-api-scan.py \
                             -t $target \
                             -r report.html \
                             -I
                         """
                    } else if (scan_type == 'Full') {
                        sh """
                             docker exec owasp \
                             zap-full-scan.py \
                             -t $target \
                             -r report.html \
                             -I
                         """
                    } else {
                        echo 'Something went wrong...'
                    }
                }
            }
        }

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
            echo 'Removing container'
            sh '''
                 docker stop owasp
                 docker rm owasp
             '''
            cleanWs()
        }
    }
}
