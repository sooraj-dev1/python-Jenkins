pipeline {
    agent any

    environment {
        // Keeps python from writing .pyc files and buffers logs
        PYTHONDONTWRITEBYTECODE = '1'
        PYTHONUNBUFFERED        = '1'
        VENV_DIR                = '.venv'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scms
            }
        }

        stage('Setup Environment') {
            steps {
                sh '''
                    python3 -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate
                    pip install --upgrade pip setuptools wheel
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                    if [ -f requirements-dev.txt ]; then pip install -r requirements-dev.txt; fi
                '''
            }
        }

        stage('Lint & Format Check') {
            steps {
                sh '''
                    . ${VENV_DIR}/bin/activate
                    pip install flake8 black
                    # Check code style and formatting
                    flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
                    black --check .
                '''
            }
        }

        stage('Test & Coverage') {
            steps {
                sh '''
                    . ${VENV_DIR}/bin/activate
                    pip install pytest pytest-cov
                    # Run tests with JUnit XML and coverage reports
                    pytest --junitxml=reports/test-results.xml \
                           --cov=. \
                           --cov-report=xml:reports/coverage.xml \
                           --cov-report=html:reports/coverage_html \
                           tests/
                '''
            }
        }

        stage('Build Package') {
            steps {
                sh '''
                    . ${VENV_DIR}/bin/activate
                    pip install build
                    python -m build
                '''
            }
        }
    }

    post {
        always {
            // Publish test results if the JUnit plugin is installed
            junit allowEmptyResults: true, testResults: 'reports/test-results.xml'

            // Publish HTML coverage report if the HTML Publisher plugin is installed
            publishHTML([
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'reports/coverage_html',
                reportFiles: 'index.html',
                reportName: 'Coverage Report'
            ])

            // Clean up workspace virtual environment to save agent disk space
            sh 'rm -rf ${VENV_DIR}'
        }
        success {
            echo "Pipeline succeeded! Artifacts built in dist/."
            archiveArtifacts artifacts: 'dist/*', allowEmptyArchive: true
        }
        failure {
            echo "Pipeline failed. Check test/lint stage outputs."
        }
    }
}