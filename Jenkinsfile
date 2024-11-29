node {
    try {
        // Validasi koneksi Docker di awal
        stage('Validate Docker Connection') {
            echo "Checking Docker connection..."
            sh 'docker info'
            echo "Docker connection validated successfully."
        }

        stage('Build') {
            // Menggunakan Docker image untuk membangun aplikasi
            docker.image('python:2-alpine').inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }

        stage('Test') {
            // Menggunakan Docker image untuk menjalankan test
            docker.image('qnib/pytest').inside {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }

            // Menyimpan hasil test dalam format junit
            junit 'test-reports/results.xml'
        }

    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        echo "Pipeline failed: ${e.message}"
        throw e
    } finally {
        echo 'Pipeline selesai.'
    }
}
