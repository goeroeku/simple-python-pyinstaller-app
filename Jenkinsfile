// https://www.jenkins.io/doc/tutorials/build-a-python-app-with-pyinstaller/

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
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    stage('Deploy') {
        try {
            unstash(name: 'compiled-results')
            sh "docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-goeroeku/sources:/src cdrx/pyinstaller-linux:python2 'pyinstaller -F add2vals.py'"
        }catch (Exception ex) {
            echo 'Error!'
        }finally{
            archiveArtifacts "sources/dist/add2vals"
            // sh "docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-goeroeku/sources:/src cdrx/pyinstaller-linux:python2 'rm -rf build dist'"
            sleep(time: 1, unit: "MINUTES")
        }
    }
}
