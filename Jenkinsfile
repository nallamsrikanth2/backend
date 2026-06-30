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
        def nexusUrl   = 'nexusserver.nsrikanth.online:8081' 
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
        stage('build') {
            steps {
                sh """
                    zip -q -r backend-${appversion}.zip * -x Jenkinsfile -x backend-${appversion}.zip
                    ls -ltr
                """
            }
        }
        stage( 'nexus artifact upload') {
            steps {
                script {
                        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: "${nexusUrl}",
        groupId: 'com.backend',
        version: "${appversion}",
        repository: 'backend',
        credentialsId: 'nexus-auth',
        artifacts: [
            [artifactId: "backend",
             classifier: '',
             file: "backend-" + "${appversion}" + '.zip',
             type: 'zip']
        ]
     )
                }
                }
            }
            stage('Trigger Deploy') {
                def params = [ string(name: 'appversion', value: "${appversion}"),]
                steps {
                    script{
                        build job: 'backend-deploy',
                        parameters: params,
                        wait: true
                    }
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