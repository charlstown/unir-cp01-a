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
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    // Run Pytest unit tests with coverage
                    sh 'python3 -m coverage run --branch --source=app --omit=app/__init__.py,app/api.py -m pytest --junitxml=result-unit.xml test/unit'
                }
                // Publish Unit test results
                junit 'result-unit.xml'
            }
        }
        stage('Rest') {
            environment {
                PYTHONPATH="/var/jenkins_home/workspace/cp_1.2/reto_1"
            }
            steps {
                // Continue the pipeline even if REST tests fail
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    sh '''
                    export FLASK_APP=app/api.py
                    echo "Simulating slow flask startup..."
                    sleep 30 &&
                    flask run &
                    python3 -m pytest --junitxml=result-rest.xml test/rest
                    '''
                }
                // Publish REST test results, even if some tests failed
                junit 'result-rest.xml'
            }
        }
        stage('Static') {
            steps {
                // Run flake8 with pylint
                sh 'python3 -m flake8 --format=pylint --exit-zero --output-file=result-flake8.out app'

                // Publish the flake8 report
                recordIssues tools: [flake8(pattern: 'result-flake8.out')], 
                             qualityGates: [
                                 [threshold: 8, type: 'TOTAL', unstable: true],
                                 [threshold: 10, type: 'TOTAL', unhealthy: true]
                             ]
            }
        }
        stage('Security Test') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    // Run bandit with custom message template
                    sh 'bandit -r . -f custom -o bandit.out --msg-template "{abspath}:{line}:{severity}:{test_id}:{msg}"'
                }

                // Publish the bandit report
                recordIssues tools: [pyLint(name: 'bandit', pattern: 'bandit.out')], 
                             qualityGates: [
                                 [threshold: 2, type: 'TOTAL', unstable: true],
                                 [threshold: 4, type: 'TOTAL', unhealthy: true]
                             ]
            }
        }
        stage('Coverage') {
            steps {
                // Generate the coverage XML report
                sh 'python3 -m coverage xml -o coverage.xml'
                
                // Publish the coverage report
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    cobertura coberturaReportFile: 'coverage.xml', 
                              lineCoverageTargets: '95,85,85', 
                              conditionalCoverageTargets: '90, 80, 80'
                }
            }
        }
    }
}
