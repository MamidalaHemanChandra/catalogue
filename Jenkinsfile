pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }

    environment { 
        Course = 'Jenkins'
    }

    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }

    stages {

        stage('Build') {
            steps {
                script {
                    sh """
                    echo "Building"
                    """
                } 
            }
        }

        stage('Test') {
            steps {
                script {
                    sh """
                    echo "Testing"
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh """
                    echo "Deploying"
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