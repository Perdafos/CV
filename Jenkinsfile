pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'No build required for static site'
            }
        }

        stage('Test') {
            steps {
                echo 'No tests defined'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Starting server on port 5060'
                bat 'python -m http.server 5060'
            }
        }
    }
}