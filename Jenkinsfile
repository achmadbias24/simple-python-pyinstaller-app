node {
    try {
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

        stage('Deliver') {
            // Menggunakan Docker image untuk melakukan delivery dengan pyinstaller
            docker.image('cdrx/pyinstaller-linux:python2').inside {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            
            // Mengarsipkan hasil artifact
            archiveArtifacts 'dist/add2vals'
        }
        
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        echo 'Pipeline selesai.'
    }
}
