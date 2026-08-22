pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonarqube-scanner'
    }

    stages {

        stage('Install Client Dependencies') {
            steps {
                dir('client') {
                    sh 'npm install'
                }
            }
        }

        stage('Install Server Dependencies') {
            steps {
                dir('server') {
                    sh 'npm install'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=MERN-ECommerce \
                        -Dsonar.projectName=MERN-ECommerce \
                        -Dsonar.sources=.
                    '''
                }
            }
        }

        stage('Docker Build Client') {
            steps {
                sh 'docker build -t mern-client ./client'
            }
        }

        stage('Docker Build Server') {
            steps {
                sh 'docker build -t mern-server ./server'
            }
        }

    }
}
