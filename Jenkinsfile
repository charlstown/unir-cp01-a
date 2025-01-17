pipeline {
    agent any
    stages {
        stage('Get Code') {
            steps {
                // Add the safe.directory and clone the repository
                sh 'git config --global --add safe.directory /var/jenkins_home/workspace/O24/test-1'
                git 'https://github.com/charlstown/unir-cp01-a.git'
            }
        }
        stage('Unit') {
            environment {
                PYTHONPATH="/var/jenkins_home/workspace/cp_1.2/reto_1"
            }
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
                    // Run Pytest unit tests
                    sh 'python3 -m pytest --junitxml=result-unit.xml test/unit'
                }
                
            }
        }
        stage('Coverage') {
            steps {
                // Run coverage with the correct source paths
                sh 'python3 -m coverage run --branch --source=app/__init__.py,app/api.py -m pytest test/unit'
                
                // Check if coverage data was collected
                sh 'python3 -m coverage report'
                
                // Generate the coverage XML report
                sh 'python3 -m coverage xml -o coverage.xml'
                
                // Publish the coverage report
                step([$class: 'CoberturaPublisher', coberturaReportFile: 'coverage.xml'])
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