node {
    withDockerContainer(image: 'python:2-alpine', toolName: 'docker') {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }
    withDockerContainer(image: 'qnib/pytest', toolName: 'docker') {
        stage('Test') {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            always {
                junit 'test-reports/results.xml'
            }
        }
    }
    stage('Deliver') {
        dir(path: env.BUILD_ID) {
            unstash(name: 'compiled-results')
            sh "docker run --rm -v ./sources:/src cdrx/pyinstaller-linux:python2 'pyinstaller -F add2vals.py'"
        }
        success {
            archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals"
            sh "docker run --rm -v ./sources:/src cdrx/pyinstaller-linux:python2 'rm -rf build dist'"
        }
    }
}
