pipeline {
    //This is pre-build section//
    agent { 
        node {
            label 'agent-1'
        }
    }
    environment {
        Project = "roboshop"
        Component = "catalogue"
        Environment = "dev"
        App_Version = ""
        Acc_ID = "064629264387"
        Region = "us-east-1"
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
                    App_Version = packageJSON.version
                    echo "App Version: ${App_Version}"
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
                def scannerHome = tool 'sonar-8.0'
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
                    withAWS(Region: 'us-east-1', credentials: 'aws-creds'){
                        sh """
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${Acc_ID}.dkr.ecr.us-east-1.amazonaws.com
                        docker build -t ${Acc_ID}.dkr.ecr.us-east-1.amazonaws.com/${Project}/${Component}:${App_Version} .
                        docker images
                        docker push ${Acc_ID}.dkr.ecr.us-east-1.amazonaws.com/${Project}/${Component}:${App_Version}
                        """
                    }
                }
            }
        }
    }

    post{
        always{
            echo 'I will always say Hello again'
            cleanws()
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


