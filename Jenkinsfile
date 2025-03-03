pipeline {
    agent none
    stages {
        stage('Checkout') {
            agent {
                docker { image 'python:2-alpine' }
            }
            steps {
                git url: 'https://github.com/arifrizqi/simple-python-pyinstaller-app.git'
            }
        }

        stage('Build') {
            agent {
                docker { image 'python:2-alpine' }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
            post {
                success { echo 'Build berhasil' }
                failure { echo 'Build gagal' }
            }
        }

        stage('Test') {
            agent {
                docker { image 'qnib/pytest' }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }

        stage('Manual Approval') {
            agent none
            steps {
                input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
            }
            post {
                aborted {
                    echo 'Pipeline dihentikan oleh user'
                }
            }
        }

        stage('Deploy') {
            agent {
                docker { image 'python:2-alpine' }
            }
            steps {
                echo 'Menjalankan aplikasi selama 1 menit'
                script {
                    sh '''
                        nohup python sources/add2vals.py > app.log 2>&1 &
                        echo $! > pid.txt
                    '''
                    
                    sleep time: 1, unit: 'MINUTES'
                    sh 'pkill -f add2vals.py || true'
                    sh 'rm pid.txt || true'
                }
            }
            post {
                success { echo 'Deploy berhasil' }
                failure { echo 'Deploy gagal' }
            }
        }
    }
}