pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        // Timeout counter starts BEFORE agent is allocated
        timeout(time: 1, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    environment {
        def appversion = ''
    }
    stages {
        stage('read the version') {
            steps {
                script {
                    def packagejson = readJSON file: 'package.json'
                    appversion = packagejson.version
                    echo "application version: $appversion"
                    }
            }
        }
        stage('plan') {
            steps {
                sh """
                   echo "this is testing"
                   npm install
                   ls -ltr
                   echo "application version: $appversion"
                """
            }
        }
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
            cleanWs()
        }
        success {
            echo "I will run when pipeline is sucess"
        }
        failure {
            echo " i will when pipeline is failure"
        }
    }
}