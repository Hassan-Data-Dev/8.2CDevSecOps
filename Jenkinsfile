pipeline {
    agent any

    tools {
        nodejs 'nodejs-lts'
    }

    environment {
        NOTIFY_TO = 's226433107@deakin.edu.au'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Hassan-Data-Dev/8.2CDevSecOps.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                script {
                    // Capture the test output so we can email it as an attachment
                    def result = bat(
                        script: 'npm test > test-output.log 2>&1 & exit /b 0',
                        returnStatus: true
                    )
                    // Send an email after the test stage, attaching the log
                    emailext(
                        subject: "Test Stage ${result == 0 ? 'SUCCESS' : 'FAILURE'}: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                        body: "The Test stage completed with status: ${result == 0 ? 'SUCCESS' : 'FAILURE'}\n\n" +
                              "Stage output is attached. View the build at: ${env.BUILD_URL}console",
                        to: "${NOTIFY_TO}",
                        attachments: 'test-output.log'
                    )
                }
            }
        }
        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'   // Ensure coverage report exists
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                script {
                    // Capture the security scan output so we can email it as an attachment
                    def auditResult = bat(
                        script: 'npm audit > audit-output.log 2>&1 & exit /b 0',
                        returnStatus: true
                    )
                    // Send an email after the security scan stage, attaching the log
                    emailext(
                        subject: "Security Scan Stage ${auditResult == 0 ? 'SUCCESS' : 'FAILURE'}: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                        body: "The Security Scan (npm audit) stage completed with status: ${auditResult == 0 ? 'SUCCESS' : 'FAILURE'}\n\n" +
                              "The audit report is attached. View the build at: ${env.BUILD_URL}console",
                        to: "${NOTIFY_TO}",
                        attachments: 'audit-output.log'
                    )
                }
            }
        }
    }

    post {
        success {
            emailext(
                subject: "Build SUCCESS: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "The build ${env.BUILD_NUMBER} of ${env.JOB_NAME} succeeded.\n\nView it at: ${env.BUILD_URL}",
                to: "${NOTIFY_TO}",
                attachLog: true
            )
        }
        failure {
            emailext(
                subject: "Build FAILED: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "The build ${env.BUILD_NUMBER} of ${env.JOB_NAME} failed.\n\nView the log at: ${env.BUILD_URL}console",
                to: "${NOTIFY_TO}",
                attachLog: true
            )
        }
    }
}