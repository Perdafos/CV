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
                    scp -P 2222 -o StrictHostKeyChecking=no -r \
                    $(ls | grep -v Jenkinsfile | grep -v .git) \
                    perdafos@103.144.209.109:/var/www/html/
                '''
            }
        }
    }
}
