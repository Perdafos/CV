pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    scp -o StrictHostKeyChecking=no -r * \
                    perdafos@103.144.209.109:/var/www/html/
                '''
            }
        }
    }
}
