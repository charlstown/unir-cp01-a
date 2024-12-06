pipeline {
    agent any
    stages {
        stage('Setup') {
            steps {
                // Add the safe.directory and clone the repository
                sh 'git config --global --add safe.directory /var/jenkins_home/workspace/O24/test-1'
                git 'https://github.com/charlstown/unir-cp01A.git'
            }
        }
        stage('Build') {
            steps {
                // No build needed
                echo 'No build needed'
                sh 'dir'
            }
        }
        stage('Unit') {
            environment {
                PYTHONPATH="/var/jenkins_home/workspace/O24/test-1"
            }
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'Failure'){
                    // Run Pytest unit tests
                    sh 'python3 -m pytest --junitxml=result-unit.xml test/unit'
                }
                
            }
        }
        stage('Rest') {
            environment {
                PYTHONPATH="/var/jenkins_home/workspace/O24/test-1"
            }
            steps {
                // Run flask app
                catchError(buildResult: 'UNSTABLE', stageResult: 'Failure'){
                    sh'''
                    export FLASK_APP=app/api.py
                    flask run &
                    sh 'python3 -m pytest --junitxml=result-rest.xml test/rest/api_test.py'
                    '''
                }
            }
        }
        stage('Results') {
            steps {
                // Get results
                junit 'result*.xml'
            }
        }
    }
}