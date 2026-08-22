pipeline {
    agent any

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