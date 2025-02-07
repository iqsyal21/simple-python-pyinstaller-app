node {
    properties([
        pipelineTriggers([pollSCM('H/2 * * * *')])
    ])

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

    stage('Deploy') {
        try {
            docker.image('python:2-alpine').inside {
                sh 'python sources/add2vals.py 5 3'

                echo 'Aplikasi berjalan selama 1 menit...'
                sleep(time: 60, unit: 'SECONDS')                 
            }
        } catch (err) {
            echo "ERROR: Deploy stage failed - ${err.getMessage()}"
            error "Deployment failed. Check logs for more details."
        }
    }
}
