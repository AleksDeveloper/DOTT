pipeline {
    agent any
    tools {
        go '1.19.4'
    }
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
    }
    stages {
        stage('Pre Test') {
            steps {
                echo 'Installing dependencies'
                sh 'go version'
                sh 'cd ./cidr_convert_api/go/'
                sh 'go install golang.org/x/lint/golint@latest'
                sh 'go install github.com/Pepegasca/goop@latest'
            }
        }
        stage('Static Code Analysis (SonarQube)') {
            steps {
                sh 'echo Here goes Static Code Analysis'
                def scannerHome = tool 'SQS 4.7.0.2747';
                withSonarQubeEnv('SonarCloud') {
                    sh '''${scannerHome}/bin/sonar-scanner \
                        -Dsonar.organization=AleksDeveloper \
                        -Dsonar.projectKey=AleksDeveloper_DOTT \
                        -Dsonar.sources=./cidr_convert_api/go/ \
                        -Dsonar.host.url=https://sonarcloud.io
                    '''
                }
            }
        }
        stage('Quality gate'){
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        stage('Example') {
            steps {
                sh 'echo ***********GO VERSION*******************'
                sh 'go version'
            }
        }
    }
}