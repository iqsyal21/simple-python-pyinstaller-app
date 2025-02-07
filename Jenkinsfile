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
        docker.image('python:2-alpine').inside {
            sh 'python sources/add2vals.py'

            echo 'Aplikasi berjalan selama 1 menit...'
            sleep(time: 60, unit: 'SECONDS') 
            
            sh 'pkill -f "python sources/add2vals.py"'
        }
    }
}
