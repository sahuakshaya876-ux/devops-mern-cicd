
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


       stage('Trivy Filesystem Scan') {
            steps {
                sh '''
                    trivy fs \
                    --scanners vuln,secret \
                    --skip-dirs node_modules \
                    --skip-dirs .git \
                    .
                '''
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

        stage('Trivy Docker Image Scan') {
    steps {
        sh '''
            trivy image --severity HIGH,CRITICAL mern-client:latest
            trivy image --severity HIGH,CRITICAL mern-server:latest
        '''
    }
}

    stage('ECR Login') {
    steps {
        sh '''
            aws ecr get-login-password --region ap-south-1 | \
            docker login --username AWS --password-stdin \
            472506472516.dkr.ecr.ap-south-1.amazonaws.com
        '''
    }
}

stage('Tag Docker Images') {
    steps {
        sh '''
            docker tag mern-client:latest \
            472506472516.dkr.ecr.ap-south-1.amazonaws.com/mern-client:latest

            docker tag mern-server:latest \
            472506472516.dkr.ecr.ap-south-1.amazonaws.com/mern-server:latest
        '''
    }
}

stage('Push Images to ECR') {
    steps {
        sh '''
            docker push \
            472506472516.dkr.ecr.ap-south-1.amazonaws.com/mern-client:latest

            docker push \
            472506472516.dkr.ecr.ap-south-1.amazonaws.com/mern-server:latest
        '''
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


