pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }

    environment { 
        Course = 'Jenkins'
        appVersion = ""
        ACC_ID = "634758830486"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"

    }

    options {
        timeout(time: 10, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }

    stages {

        stage('Read App Version') {
            steps {
                script {
                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "App Version is: ${appVersion}"
                } 
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh """
                    npm install 
                    """
                }
            }
        }

        stage('Unit Test') {
            steps {
                script {
                    sh """
                    npm test 
                    """
                }
            }
        }

       /*  stage('SonarQube analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonar-7.0'
                    withSonarQubeEnv('sonar-server') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        } */

        /* stage('Check Dependabot Alerts') {
            environment {
                GITHUB_OWNER = 'MamidalaHemanChandra'
                GITHUB_REPO  = 'catalogue'
                GITHUB_TOKEN = credentials('github-token')
            }

            steps {
                script {

                    def response = sh(
                        script: '''
                            curl -s \
                            -H "Accept: application/vnd.github+json" \
                            -H "Authorization: Bearer $GITHUB_TOKEN" \
                            -H "X-GitHub-Api-Version: 2022-11-28" \
                            https://api.github.com/repos/$GITHUB_OWNER/$GITHUB_REPO/dependabot/alerts
                        ''',
                        returnStdout: true
                    ).trim()

                    def alerts = readJSON text: response

                    def blockingAlerts = alerts.findAll {
                        it.state == "open" &&
                        (it.security_advisory.severity == "critical" ||
                        it.security_advisory.severity == "high")
                    }

                    if (blockingAlerts) {
                        error("Critical/High Dependabot alerts found")
                    }

                    echo "No blocking alerts found"
                }
            }
        } */


        stage('Build Image ECR') {
            steps {
                script {
                    withAWS(region:'us-east-1',credentials:'aws-creds') {
                        sh """
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                        docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                        docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                        """
                    }
                    
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                script {
                    sh """
                    trivy image --severity HIGH,CRITICAL,MEDIUM --pkg-types os ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
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