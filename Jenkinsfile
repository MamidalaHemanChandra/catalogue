pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }

    environment { 
        Course = 'Jenkins'
        appVersion = ""
    }

    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }

    stages {

        // Install Plugin: Pipeline Utility Steps
        stage('App Version') {
            steps {
                script {
                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "App Version is: ${appVersion}"
                } 
            }
        }

        stage('Install dependencies') {
            steps {
                script {
                    sh """
                    npm install  
                    """
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    sh """
                    docker build -t catalogue:${appVersion} .
                    docker images
                    """
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
            echo 'I will run if success'
        }
        failure {
            echo 'I will not run if failure'
        }
        aborted {
            echo 'Pipeline is aborted'
        }
    }
}