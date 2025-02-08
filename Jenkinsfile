node {
    properties([
        pipelineTriggers([pollSCM('H/2 * * * *')])
    ])

    stage('Build') {
        steps {
            docker.image('python:2-alpine').inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
    }

    stage('Test') {
        steps {
            docker.image('qnib/pytest').inside {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
                junit 'test-reports/results.xml'
            }
        }
    }

    stage('Deploy') {
        steps {
            script {
                def userInput = input message: 'Lanjutkan ke tahap Deploy?', 
                    parameters: [
                        choice(name: 'Pilih', choices: ['Ya', 'Tidak'], description: 'Pilih "Ya" untuk melanjutkan deploy atau "Tidak" untuk mengakhiri pipeline')
                    ]

                if (userInput == 'Ya') {
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
                } else {
                    echo "Deployment dibatalkan oleh user."
                }
            }
        }
    }
}
