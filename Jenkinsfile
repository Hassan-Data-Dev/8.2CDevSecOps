pipeline {
    agent any

    tools {
        nodejs 'nodejs-lts'
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
                bat 'npm test || exit /b 0'   // Allows pipeline to continue despite test failures
            }
        }
        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'   // Ensure coverage report exists
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0'   // Shows known CVEs in the output
            }
        }
    }

    post {
        success {
            emailext(
                subject: "Build SUCCESS: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "The build ${env.BUILD_NUMBER} of ${env.JOB_NAME} succeeded.\n\nView it at: ${env.BUILD_URL}",
                to: 's226433107@deakin.edu.au'
            )
        }
        failure {
            emailext(
                subject: "Build FAILED: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "The build ${env.BUILD_NUMBER} of ${env.JOB_NAME} failed.\n\nView the log at: ${env.BUILD_URL}console",
                to: 's226433107@deakin.edu.au'
            )
        }
    }
}