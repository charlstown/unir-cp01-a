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
                sh 'python3 -m coverage run --branch --source=app --omit=app/__init__.py,app/api.py -m pytest test/unit'

                // Generate the coverage XML report
                sh 'python3 -m coverage xml -o coverage.xml'
                
                // Publish the coverage report
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    cobertura coberturaReportFile: 'coverage.xml', 
                              lineCoverageTargets: '100, 0, 90', 
                              conditionalCoverageTargets: '100, 0, 80'
                }
            }
        }
        stage('Static') {
            steps {
                // Run flake8 with pylint
                sh 'python3 -m flake8 --format=pylint --exit-zero --output-file=result-flake8.out app'

                // Publish the flake8 report
                recordIssues tools: [flake8(pattern: 'result-flake8.out')], 
                             qualityGates: [
                                 [threshold: 12, type: 'TOTAL', unstable: true],
                                 [threshold: 15, type: 'TOTAL', unstable: false]
                             ]
            }
        }
        stage('Security') {
            steps {
                // Run bandit with custom message template
                catchError(buildResult: 'UNSTABLE', stageResult: 'SUCCESS') {
                    sh 'bandit -r . -f custom -o bandit.out --msg-template "{abspath}:{line}: [{test_id}] {msg}"'
                }

                // Publish the bandit report
                recordIssues tools: [pyLint(name: 'bandit', pattern: 'bandit.out')], 
                             qualityGates: [
                                 [threshold: 4, type: 'TOTAL', unstable: true],
                                 [threshold: 8, type: 'TOTAL', unstable: false]
                             ]
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
