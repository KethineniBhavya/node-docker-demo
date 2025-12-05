
pipeline {
    agent any

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
}

