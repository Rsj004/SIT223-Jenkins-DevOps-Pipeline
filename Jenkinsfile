pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Redback-Operations/redback-fit-backend.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                bat 'python -m pip install -r requirements.txt'
                bat 'python -m pip install pytest'
                bat 'python -m pip install "setuptools<81"'
                bat 'python -m pip install "Werkzeug<3"'
                bat 'python -m pip install bandit'
                bat 'python -m pip install flake8'
                
            }
        }
        stage('Build') {
            steps {
                bat 'powershell Compress-Archive -Path *.py,requirements.txt -DestinationPath redback-backend.zip -Force'
                archiveArtifacts artifacts: 'redback-backend.zip'
           }
        }
        stage('Run Tests') {
            steps {
                bat 'python -m pytest tests'
            }
        }
        stage('Security Scan') {
            steps {
                bat 'python -m bandit -r . -lll'
            }
        }
        stage('Code Quality') {
             steps {
                  bat 'python -m flake8 . --select=E9,F63,F7,F82 --count --statistics'
            }   
        }
        stage('Deploy') {
             steps {
                  bat 'echo Starting Flask application in staging environment...'
                  bat 'start /B python app.py > staging.log 2>&1'
                  bat 'powershell -Command "Start-Sleep -Seconds 5"'
                  bat 'powershell -Command "$response = Invoke-WebRequest -Uri http://127.0.0.1:5000 -UseBasicParsing; if ($response.StatusCode -eq 200) { Write-Host \'STAGING CHECK PASSED: Application returned HTTP 200\' } else { exit 1 }"'
             }
         }
         stage('Integration Test') {
              steps {
                   bat 'echo Running integration test against staging API...'
                  bat 'powershell -Command "$response = Invoke-WebRequest -Uri http://127.0.0.1:5000/api/test -UseBasicParsing; if ($response.StatusCode -eq 200 -and $response.Content -match \'Hello, API!\') { Write-Host \'INTEGRATION TEST PASSED: Staging API returned the expected response\' } else { Write-Host \'INTEGRATION TEST FAILED\'; exit 1 }"'
              }
          }
         stage('Release') {
             steps {
                   bat 'echo Promoting validated application to production environment...'
                   bat 'if not exist production mkdir production'
                   bat 'copy /Y redback-backend.zip production\\redback-backend-v1.0.zip'
                   archiveArtifacts artifacts: 'production/redback-backend-v1.0.zip'

                   bat 'set PORT=5001 && start /B python app.py > production.log 2>&1'
                   bat 'powershell -Command "Start-Sleep -Seconds 5"'

                   bat 'powershell -Command "$response = Invoke-WebRequest -Uri http://127.0.0.1:5001 -UseBasicParsing; if ($response.StatusCode -eq 200) { Write-Host \'PRODUCTION DEPLOYMENT PASSED: Application returned HTTP 200\' } else { exit 1 }"'
            }
          }
          stage('Monitoring') {
               steps {
                     bat 'echo Monitoring production application health...'
                     bat 'powershell -Command "try { $response = Invoke-WebRequest -Uri http://127.0.0.1:5001 -UseBasicParsing; if ($response.StatusCode -eq 200) { Write-Host \'MONITORING PASSED: Production application is healthy and available\' } else { Write-Host \'ALERT: Production application is unhealthy\'; exit 1 } } catch { Write-Host \'ALERT: Production application is unavailable\'; exit 1 }"'
              }
          }
     }
}
