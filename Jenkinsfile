pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps { echo 'Static site - no build step' }
        }

        stage('Archive') {
            steps {
                // store the site files as artifacts
                archiveArtifacts artifacts: '**/*', excludes: '.git/**', fingerprint: true
            }
        }

        stage('Smoke Test (port 5060)') {
            steps {
                script {
                    if (isUnix()) {
                        sh '''#!/bin/bash
PY=$(command -v python3 || command -v python || true)
if [ -z "$PY" ]; then
    echo "No python available on agent; skipping smoke test"
    exit 0
fi
echo "Starting temporary server with $PY on port 5060"
$PY -m http.server 5060 > /tmp/cv_server.log 2>&1 &
PID=$!
sleep 2
if command -v curl >/dev/null 2>&1; then
    curl -sfS http://localhost:5060 >/dev/null || (cat /tmp/cv_server.log; kill $PID; exit 1)
else
    wget -qO- http://localhost:5060 >/dev/null || (cat /tmp/cv_server.log; kill $PID; exit 1)
fi
kill $PID || true
'''
                    } else {
                        bat '''
powershell -NoProfile -Command ^
    $py = (Get-Command python -ErrorAction SilentlyContinue).Path; ^
    if(-not $py){ Write-Host 'python not found, skipping smoke test'; exit 0 } ^
    $p = Start-Process -FilePath $py -ArgumentList '-m','http.server','5060' -PassThru; ^
    Start-Sleep -Seconds 2; ^
    try { Invoke-WebRequest -UseBasicParsing -Uri http://localhost:5060 -ErrorAction Stop | Out-Null } ^
    finally { $p | Stop-Process -Force -ErrorAction SilentlyContinue }
'''
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}