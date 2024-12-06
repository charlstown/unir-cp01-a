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
            steps {
                // Run Pytest unit tests
                sh 'python3 -m pytest --junitxml=result-unit.xml test/unit'
            }
        }
        stage('Results') {
            steps {
                // Your Unit steps go here
                junit 'result*.xml'
            }
        }
        stage('Rest') {
            steps {
                // Your Unit steps go here
                sh 'python3 -m pytest --junitxml=result-rest.xml test/rest/api_test.py'
            }
        }
    }
}