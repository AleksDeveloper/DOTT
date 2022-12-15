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
                    echo '*******BUILD*********'
                    cd ./cidr_convert_api/go/
                    pwd
                    whoami
                    docker build . -t aleks-devops
                    docker tag aleks-devops alejandrodjc/aleks-devops
                    docker push alejandrodjc/aleks-devops
                 '''
            }
          
        }
        stage('Unit Tests'){
            steps{
                echo '*****VETTING******'
                    //(just first time used: go mod init example.com/m)
                    //(just first time used: go mod tidy)
                sh '''
                    cd ./cidr_convert_api/go/
                    go vet .
                '''
                
                echo '*****UNIT TESTING*****'
                sh '''cd ./cidr_convert_api/go/
                '''
                script{
                    try{
                        sh '''
                        pwd
                        ls -la
                        cd cidr_convert_api/go/
                        go test -cover -coverprofile='cover.out'
                        '''
                    }catch(error){
                        echo error.getMessage()
                    }
                }
                echo currentBuild.result
                sh '''
                    pwd
                    ls -la
                    cd cidr_convert_api/go/
                    go tool cover -html=cover.out
                '''

                /*echo '*****UNIT TESTING*****'
                sh '''
                    cd ./cidr_convert_api/go/
                    go test -coverprofile='cover.out'
                    go tool cover -html=cover.out
                '''*/
            }
        }
        stage('Linting'){
            steps{
                sh 'echo *****LINTING******'
                //sh 'golangci-lint run ./cidr_convert_api/go/'
            }
        }
        stage('Deployment'){
            steps{
                sh 'echo ************DEPLOYMENT*************'
                sh '''
                docker pull alejandrodjc/aleks-devops
                docker run -d --name goproject aleks-devops
                '''
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