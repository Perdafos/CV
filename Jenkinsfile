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
                    scp -P 2222 -o StrictHostKeyChecking=no \
                    -r dafa.png download.html index.html style.css \
                    perdafos@103.144.209.109:/var/www/html/
                '''
            }
        }
    }
}
