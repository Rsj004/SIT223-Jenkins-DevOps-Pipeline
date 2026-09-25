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
                bat 'python -m flake8 . --count --statistics --exit-zero'
            }
        }

        stage('Deploy') {
            steps {
                bat 'if not exist deployment mkdir deployment'
                bat 'copy /Y redback-backend.zip deployment\\redback-backend.zip'
                bat 'dir deployment'
            }
        }

        stage('Release') {
            steps {
                bat 'if not exist release mkdir release'
                bat 'copy /Y redback-backend.zip release\\redback-backend-v1.0.zip'
                archiveArtifacts artifacts: 'release/redback-backend-v1.0.zip'
                bat 'dir release'
            }
        }

        stage('Monitoring') {
            steps {
                bat 'echo Checking deployed application artefact...'
                bat 'if exist deployment\\redback-backend.zip (echo STATUS: Deployment artefact is available) else (echo STATUS: Deployment artefact is missing & exit /b 1)'
                bat 'dir deployment'
            }
        }
    }
}
