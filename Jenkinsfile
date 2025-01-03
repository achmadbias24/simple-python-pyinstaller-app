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
                        id: 'userInput',
                        message: 'Lanjutkan ke tahap Deploy?',
                        ok: 'Proceed', // Tombol untuk melanjutkan
                        submitter: 'Abort' // Tombol untuk membatalkan
                    )

                    echo 'Deploying application...'
                    timeout(time: 1, unit: 'MINUTES') {
                        echo 'Process is running for 1 minute...'
                        sleep(time: 60, unit: 'SECONDS') // Simulasi proses selama 1 menit
                    }
                    echo 'Deployment process completed.'
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