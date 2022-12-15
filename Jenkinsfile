pipeline {
    agent any
    tools {
        go '1.19.4'
    }
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
        scannerHome = tool 'SQS 4.7.0.2747';
    }
    stages {
        stage('Pre Test') {
            steps {
                echo 'Installing dependencies'
                sh 'go version'
                sh 'docker --version'
                sh 'cd ./cidr_convert_api/go/'
                sh 'go install golang.org/x/lint/golint@latest'
                sh 'go install github.com/Pepegasca/goop@latest'
            }
        }
        stage('Static Code Analysis (SonarQube)') {
            steps {
                sh 'echo Here goes Static Code Analysis'
                withSonarQubeEnv('SonarCloud') {
                    sh '''${scannerHome}/bin/sonar-scanner \
                        -Dsonar.organization=aleksdeveloper \
                        -Dsonar.projectKey=aleksdeveloper_DOTT \
                        -Dsonar.sources=./cidr_convert_api/go/ \
                        -Dsonar.host.url=https://sonarcloud.io
                    '''
                }
            }
        }
        /*stage('Quality gate'){
            steps {
                waitForQualityGate abortPipeline: true
            }
        }*/
        stage('Build'){
            steps{
                sh '''
                    echo 'BUILD'
                    cd ./cidr_convert_api/go/
                    pwd
                    docker build . -t aleks-devops
                    docker tag aleks-devops aleksdeveloper/aleks-devops
                    docker push aleksdeveloper/aleks-devops
                 '''
            }
          
        }
        stage('Unit Tests'){
            steps{
                echo '*****VETTING******'
                sh '''
                    cd ./cidr_convert_api/go/
                    go vet .
                '''
                echo '*****UNIT TESTING*****'
                sh '''
                    cd ./cidr_convert_api/go/
                    go test -cover -coverprofile='cover.out'
                '''
                echo '*****LINTING******'
                sh 'golint ./cidr_convert_api/go/' 
            }
        }
        stage('Deployment'){
            steps{
                sh 'echo Here goes the Deployment'
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