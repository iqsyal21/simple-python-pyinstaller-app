node {
    properties([
        pipelineTriggers([pollSCM('H/2 * * * *')]) // Cek commit baru setiap 2 menit
    ])

    try {
        stage('Build') {
            docker.image('python:2-alpine').inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }

        stage('Test') {
            docker.image('qnib/pytest').inside {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
                junit 'test-reports/results.xml'
            }
        }
    } catch (err) {
        error "Pipeline failed: ${err.message}"
    }
}
