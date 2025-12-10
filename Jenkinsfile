pipeline {
    agent any
 
    environment {
        EMAIL_RECIPIENTS = 'bhavyakethineni@gmail.com' // comma-separated
    }
 
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
 
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t node-app .'
            }
        }
 
        stage('Run Docker Container') {
            steps {
                sh '''
                  # Stop any previous container if present
                  docker rm -f node-app || true
 
                  # Start container and keep it running even after reboot
                  docker run -d --restart unless-stopped -p 3000:3000 --name node-app node-app
                '''
            }
        }
    }
 
    post {
        // Send only when the whole pipeline fails
        failure {
            mail to: "${env.EMAIL_RECIPIENTS}",
                 subject: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: """Build failed.
 
Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
Branch: ${env.GIT_BRANCH ?: 'n/a'}
 
Console Log: ${env.BUILD_URL}console
Artifacts:   ${env.BUILD_URL}artifact/
"""
        }
 
        // Optional: send success only on recovery to reduce noise
        success {
            script {
                if (currentBuild.previousBuild?.result == 'FAILURE') {
                    mail to: "${env.EMAIL_RECIPIENTS}",
                         subject: "✅ RECOVERY: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                         body: """Build recovered to SUCCESS.
 
Details: ${env.BUILD_URL}
"""
                }
            }
        }
 
        // Optional: always add a footer in the job log
        always {
            echo "Notification evaluated: ${currentBuild.currentResult}"
        }
    }
}

