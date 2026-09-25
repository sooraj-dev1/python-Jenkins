pipeline {
    agent any

    environment {
        PYTHONDONTWRITEBYTECODE = '1'
        PYTHONUNBUFFERED        = '1'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest test_app.py -v --junitxml=results.xml
                '''
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'results.xml'
            sh 'rm -rf venv'
        }
    }
}