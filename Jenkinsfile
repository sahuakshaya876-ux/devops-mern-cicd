pipeline {

    agent any

    environment {
        SCANNER_HOME = tool 'sonarqube-scanner'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Versions') {
            steps {
                sh '''
                    node --version
                    npm --version
                    git --version
                    docker --version
                '''
            }
        }

        stage('Install Client Dependencies') {
            steps {
                dir('client') {
                    sh '''
                        npm install
                    '''
                }
            }
        }

        stage('Install Server Dependencies') {
            steps {
                dir('server') {
                    sh '''
                        npm install
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    withCredentials([
                        string(
                            credentialsId: 'SonarQube-Token',
                            variable: 'SONAR_TOKEN'
                        )
                    ]) {
                        sh '''
                            $SCANNER_HOME/bin/sonar-scanner \
                            -Dsonar.projectKey=MERN-ECommerce \
                            -Dsonar.projectName=MERN-ECommerce \
                            -Dsonar.sources=. \
                            -Dsonar.token=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Docker Build Client') {
            steps {
                sh '''
                    docker build \
                    -t mern-client:latest \
                    ./client
                '''
            }
        }

        stage('Docker Build Server') {
            steps {
                sh '''
                    docker build \
                    -t mern-server:latest \
                    ./server
                '''
            }
        }

        stage('Docker Images') {
            steps {
                sh '''
                    docker images
                '''
            }
        }
    }

    post {
        success {
            echo 'PIPELINE SUCCESSFUL'
        }

        failure {
            echo 'PIPELINE FAILED'
        }
    }
}
