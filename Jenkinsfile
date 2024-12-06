pipeline {
    agent any
    stages {
        stage('Setup') {
            steps {
                script {
                    // Add the safe.directory command
                    sh 'git config --global --add safe.directory /var/jenkins_home/workspace/O24/test-1'
                }
            }
        }
        stage('Build') {
            steps {
                // Your build steps go here
                git 'https://github.com/charlstown/unir-cp01A.git'
            }
        }
        stage('Unit') {
            steps {
                // Your Unit steps go here
                sh 'python3 -m pytest test/unit/calc_test.py'
                sh 'python3 -m pytest test/unit/util_test.py'
            }
        }
        stage('Rest') {
            steps {
                // Your Unit steps go here
                sh 'python3 -m pytest test/rest/api_test.py'
            }
        }
    }
}