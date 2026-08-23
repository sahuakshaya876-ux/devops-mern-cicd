
pipeline {

    agent any

   
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

        stage('Docker Build Client') {
            steps {
                sh 'docker build -t mern-client:latest ./client'
            }
        }

        stage('Docker Build Server') {
            steps {
                sh 'docker build -t mern-server:latest ./server'
            }
        }

        stage('Docker Images') {
            steps {
                sh 'docker images'
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


