pipeline {
    agent any

    // Variabel yang dapat dikonfigurasi
    environment {
        APP_NAME = 'cv-static-deploy'     // Nama kontainer/aplikasi
        // Nginx defaultnya berjalan di port 80 di dalam kontainer
        CONTAINER_PORT = 80               
        // Port yang diminta user (Port di host server)
        HOST_PORT = 8088                  
        DOCKER_REGISTRY = ''              // Kosongkan jika tidak push ke registry
        DOCKER_IMAGE_NAME = "cv-static:${env.BUILD_NUMBER}" // Nama image lokal
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                echo "Mengambil kode dari https://github.com/Perdafos/CV"
                // Mengambil kode dari repositori user
                git branch: 'main', url: 'https://github.com/Perdafos/CV'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Membangun image dengan tag: ${env.DOCKER_IMAGE_NAME}"
                    // Membangun image Docker menggunakan Dockerfile di root proyek
                    // Perintah ini akan menggunakan Dockerfile yang Anda sediakan di langkah berikutnya
                    docker.build(env.DOCKER_IMAGE_NAME, "--pull .") 
                }
            }
        }

        stage('Deploy to Host') {
            // Asumsi: Jenkins Agent memiliki akses ke Docker daemon
            steps {
                script {
                    echo "Melakukan deployment ke Host di port ${env.HOST_PORT}..."
                    
                    // 1. Hentikan dan hapus kontainer lama (jika ada)
                    sh "docker stop ${env.APP_NAME} || true" 
                    sh "docker rm ${env.APP_NAME} || true" 

                    // 2. Jalankan kontainer baru
                    // -d: detached mode
                    // --name: nama kontainer
                    // -p: mapping port (HOST_PORT:CONTAINER_PORT -> 5060:80)
                    sh """
                        docker run -d \
                        --name ${env.APP_NAME} \
                        -p ${env.HOST_PORT}:${env.CONTAINER_PORT} \
                        ${env.DOCKER_IMAGE_NAME}
                    """
                    echo "Deployment berhasil. Aplikasi berjalan di http://103.144.209.109:${env.HOST_PORT}"
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline selesai.'
        }
        success {
            echo 'Deployment Berhasil di Port 5060! 🎉'
        }
        failure {
            echo 'Deployment Gagal! ❌'
        }
    }
}