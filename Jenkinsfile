pipeline {
    agent any
    stages {
        stage('Setup') {
            steps {
                // Configurar entorno y clonar repositorio
                sh 'git config --global --add safe.directory /var/jenkins_home/workspace/O24/test-1'
                git 'https://github.com/charlstown/unir-cp01-a.git'
            }
        }
        stage('Build') {
            steps {
                echo '[Build] No build needed'
                sh '''
                ls -la
                echo $WORKSPACE
                '''
            }
        }
        stage('Start Flask') {
            steps {
                script {
                    // Iniciar Flask en segundo plano
                    sh '''
                    export FLASK_APP=app/api.py
                    echo "Starting Flask server..."
                    flask run --host=127.0.0.1 --port=5000 > flask.log 2>&1 &
                    echo $! > flask.pid
                    '''

                    // Comprobación de readiness
                    sh '''
                    echo "Waiting for Flask to be ready..."
                    for i in {1..10}; do
                        if curl -s http://127.0.0.1:5000 > /dev/null; then
                            echo "Flask is up and running!"
                            break
                        fi
                        echo "Flask not ready yet, retrying in 3 seconds..."
                        sleep 3
                    done
                    '''
                }
            }
        }
        stage('Tests') {
            parallel {
                stage('Unit') {
                    environment {
                        PYTHONPATH="/var/jenkins_home/workspace/O24/cp1-1-dev"
                    }
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                            sh 'python3 -m pytest --junitxml=result-unit.xml test/unit'
                        }
                    }
                }
                stage('Rest') {
                    environment {
                        PYTHONPATH="/var/jenkins_home/workspace/O24/cp1-1-dev"
                    }
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                            sh 'python3 -m pytest --junitxml=result-rest.xml test/rest'
                        }
                    }
                }
            }
        }
        stage('Stop Flask') {
            steps {
                sh '''
                echo "Stopping Flask server..."
                kill $(cat flask.pid)
                rm flask.pid
                '''
            }
        }
        stage('Results') {
            steps {
                junit 'result*.xml'
            }
        }
    }
}