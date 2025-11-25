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
                withCredentials([usernamePassword(credentialsId: 'prod-server', 
                            usernameVariable: 'USER',
                            passwordVariable: 'PASS')]) {
                    sh '''
                        scp -P 2222 -o StrictHostKeyChecking=no \
                        -r dafa.png download.html index.html style.css \
                        $USER@$PASSWORD@103.144.209.109:/var/www/html/
                    '''
                }
            }
        }
    }
}
