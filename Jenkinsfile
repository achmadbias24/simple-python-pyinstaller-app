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

        stage('Deliver') {
            steps {
                script {
                    echo "Delivering application..."
                    docker.image('cdrx/pyinstaller-linux:python2').inside {
                        sh 'pyinstaller --onefile sources/add2vals.py'
                    }
                }
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
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