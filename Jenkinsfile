pipeline {
    agent any
    environment {
        // Fixed serve port requested by user
        SERVE_PORT = '5060'
    }

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
                        stage('Archive') {
                            steps {
                                // store the site files as artifacts
                                archiveArtifacts artifacts: '**/*', excludes: '.git/**', fingerprint: true
                            }
                        }

                        stage('Deploy / Serve (persistent)') {
                            steps {
                                script {
                                    // Attempt to start a persistent static server on the agent so the site becomes reachable
                                    if (isUnix()) {
                                        sh '''#!/bin/bash
                # Stop previous server if pid file exists
                if [ -f /tmp/cv_server.pid ]; then
                    kill $(cat /tmp/cv_server.pid) 2>/dev/null || true
                    rm -f /tmp/cv_server.pid
                fi
                PY=$(command -v python3 || command -v python || true)
                if [ -z "$PY" ]; then
                    echo "No python available on agent; cannot start persistent server"
                    exit 0
                fi
                echo "Starting persistent server with $PY on port $SERVE_PORT (serving $WORKSPACE)"
                nohup $PY -m http.server $SERVE_PORT >/var/log/cv-server.log 2>&1 &
                echo $! > /tmp/cv_server.pid
                echo "Server started, pid=$(cat /tmp/cv_server.pid)"
                '''
                                    } else {
                                        bat '''
                REM stop previous if exists
                if exist C:\cv_server_pid.txt (
                    for /F "usebackq tokens=*" %%p in (C:\cv_server_pid.txt) do taskkill /PID %%p /F
                    del /F /Q C:\cv_server_pid.txt
                )
                powershell -NoProfile -Command ^
                    $py=(Get-Command python -ErrorAction SilentlyContinue).Path; ^
                    if(-not $py){ Write-Host 'python not found on agent; cannot start persistent server'; exit 0 } ^
                    $p=Start-Process -FilePath $py -ArgumentList '-m','http.server','5060' -PassThru; ^
                    $p.Id | Out-File -FilePath C:\cv_server_pid.txt -Encoding ASCII; ^
                    Write-Host "Started server, pid: $($p.Id)"
                '''
                                    }
                                }
                            }
                        }