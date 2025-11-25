pipeline {
    agent any

    environment {
        SERVER_USER = 'perdafos'
        SERVER_IP   = '103.144.209.109'
        TARGET_DIR  = '/var/www/html'   // ubah sesuai folder web kamu
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Server via SCP') {
            steps {
                sh """
                scp -o StrictHostKeyChecking=no -r ./ ./\$SERVER_USER@\$SERVER_IP:\$TARGET_DIR
                """
            }
        }
    }
}
