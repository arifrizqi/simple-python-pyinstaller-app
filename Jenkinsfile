node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            checkout scm
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            checkout scm
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        always {
            junit 'test-reports/results.xml'
        }
    }
    stage('Deploy') {
        agent {
            docker {
                image 'python:3.9'
                args '-u root'
            }
        }
        steps {
            checkout scm
            sh 'pip install pyinstaller'
            sh 'pyinstaller --onefile sources/add2vals.py'
            sleep time: 1, unit: 'MINUTES'
            echo 'Pipeline has finished successfully.'
        }
        post {
            success {
                archiveArtifacts 'dist/add2vals'
            }
        }
    }
}
