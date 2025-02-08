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

    stage('Manual Approve') {
        script {
            def userInput = input message: 'Lanjutkan ke tahap Deploy?', 
                parameters: [
                    choice(name: 'Pilih', choices: ['Ya', 'Tidak'], description: 'Pilih "Ya" untuk lanjut deploy, atau "Tidak" untuk selesai.')
                ]

            if (userInput == 'Tidak') {
                echo "Deploy dihentikan oleh user."
                docker.image('python:2-alpine').inside {
                    sh './jenkins/scripts/kill.sh'
                }
                error "Pipeline dihentikan sesuai permintaan."
            }
        }
    }

    stage('Deploy') {
        try {
            docker.image('python:2-alpine').inside {
                sh 'python sources/add2vals.py 5 3'

                echo 'Aplikasi berjalan selama 1 menit...'
                sleep(time: 60, unit: 'SECONDS')
                
                sh './jenkins/scripts/kill.sh'
            }
        } catch (err) {
            echo "ERROR: Deploy stage failed - ${err.getMessage()}"
            error "Deployment failed. Check logs for more details."
        }
    }
}
