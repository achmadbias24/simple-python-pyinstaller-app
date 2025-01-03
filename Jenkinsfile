pipeline {
    agent any
    stages {
        stage('Validate Docker Connection') {
            steps {
                script {
                    echo "Checking Docker connection..."
                    sh 'docker info'
                    echo "Docker connection validated successfully."
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo "Building application..."
                    docker.image('python:2-alpine').inside {
                        sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Running tests..."
                    docker.image('qnib/pytest').inside {
                        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
                    }
                }
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def userInput = input(
                        id: 'userInput', message: 'Lanjutkan ke tahap Deploy?', parameters: [
                            [$class: 'BooleanParameterDefinition', defaultValue: true, description: 'Proceed', name: 'Continue'],
                            [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Abort', name: 'Abort']
                        ]
                    )
                    if (userInput['Continue']) {
                        echo 'Deploying application...'
                        timeout(time: 1, unit: 'MINUTES') {
                            sh 'echo "Deploying..."'
                        }
                        echo 'Deployment completed within 1 minute.'
                    } else {
                        error 'Deployment aborted by user.'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
        always {
            echo 'Pipeline selesai.'
        }
    }
}