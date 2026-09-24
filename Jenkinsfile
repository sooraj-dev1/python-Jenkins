pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                bat 'pip install -r requirements.txt'
            }
        }

        stage('Test') {
            steps {
                bat 'pytest'
            }
        }

        stage('Build') {
            steps {
                bat 'mkdir build'
                bat 'copy app.py build\\'
                bat 'copy requirements.txt build\\'
            }
        }
    }
}