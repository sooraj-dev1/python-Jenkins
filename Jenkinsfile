pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scms
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                    python -m venv venv
                    call venv\\Scripts\\activate.bat
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                bat '''
                    call venv\\Scripts\\activate.bat
                    pytest test_app.py -v --junitxml=results.xml
                '''
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'results.xml'
            bat 'rmdir /s /q venv'
        }
    }
}