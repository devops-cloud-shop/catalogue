pipeline {
    //This is pre-build section//
    agent { 
        node {
            label 'agent-1'
        }
    }
    environment {
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
        ENVIRONMENT = "dev"
        APP_VERSION = "1.0.0"
        ACC_ID = "064629264387"
        REGION = "us-east-1"
    }
    options{
        timeout(time: 30, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }
    parameters {
        string(name: 'App_Version', description: 'Which app version you want to deploy')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'Pick something')
    }
    //Build section//
    stages{
        stage('Read Version'){
            steps{
                script{
                    def packageJSON = readJSON file: 'package.json'
                    APP_VERSION = packageJSON.version
                    echo "App Version: ${APP_VERSION}"
                }
            }

        }
        stage('Install Dependencies'){
            steps{
                script{
                    sh """
                        npm install
                    """    
                }
            }
        }
        stage('Unit Test'){
            steps{
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        //Here you need to select scanner tool and send analysis to server//
        stage('sonar_scan'){
            environment{
                def scannerHome = tool 'sonar-5.0'
            }
            steps{
                script{
                    withSonarQubeEnv('sonar-server'){
                        sh "${scannerHome}/bin/sonar-scanner"
                    }

                }
            }
        }
        stage('Quality Gates'){
            steps{
                timeout(time: 1, unit: 'HOURS'){
                    //wait for quality gate status, 
                    //waitForQualityGate abortPipeline: true will fail the jenkins job if quality gate fails
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Build'){
            steps{
                script{
                    withAWS(region: 'us-east-1', credentials: 'aws-creds'){
                        sh """
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                        docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${APP_VERSION} .
                        docker images
                        docker push ${Acc_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${APP_VERSION}
                        """
                    }
                }
            }
        }
    }
//this is post build section//
    post{
        always{
            echo 'I will always say Hello again'
            cleanWs()
        }
        success{
            echo 'I will run if success'
        }
        failure{
            echo 'I will fail if failure'
        }
        aborted{
            echo 'pipeline is aborted'
        }
    }
}


