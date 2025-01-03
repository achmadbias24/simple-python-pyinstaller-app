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

        stage('Manual Approval') {
            steps {
                script {
                    def proceed = false
                    try {
                        input(
                            id: 'userInput',
                            message: 'Lanjutkan ke tahap Deploy?',
                            ok: 'Proceed' // Tombol untuk melanjutkan
                        )
                        proceed = true
                    } catch (e) {
                        echo 'Pipeline aborted by user.'
                        error 'Pipeline terminated.'
                    }

                    if (proceed) {
                        echo 'User approved. Proceeding to Deploy stage...'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sleep(time: 60, unit: 'SECONDS') // Simulasi proses selama 1 menit
                echo 'Deployment process completed.'
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