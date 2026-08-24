
pipeline {

    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '472506472516'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"

        // Backend API
        VITE_API_URL = 'http://13.235.33.67:5000'
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
                    aws --version
                    trivy --version
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
                sh '''
                    docker build \
                    --build-arg VITE_API_URL="$VITE_API_URL" \
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
                    aws ecr get-login-password --region "$AWS_REGION" | \
                    docker login \
                    --username AWS \
                    --password-stdin "$ECR_REGISTRY"
                '''
            }
        }

        stage('Tag Docker Images') {
            steps {
                sh '''
                    docker tag mern-client:latest \
                    "$ECR_REGISTRY/mern-client:latest"

                    docker tag mern-server:latest \
                    "$ECR_REGISTRY/mern-server:latest"
                '''
            }
        }

        stage('Push Images to ECR') {
            steps {
                sh '''
                    docker push "$ECR_REGISTRY/mern-client:latest"
                    docker push "$ECR_REGISTRY/mern-server:latest"
                '''
            }
        }


        stage('Deploy to EC2') {
    steps {
        sh '''
            echo "Logging into ECR..."

            aws ecr get-login-password --region "$AWS_REGION" | \
            docker login \
            --username AWS \
            --password-stdin "$ECR_REGISTRY"

            echo "Pulling latest images..."

            docker pull "$ECR_REGISTRY/mern-client:latest"
            docker pull "$ECR_REGISTRY/mern-server:latest"

            echo "Stopping old containers..."

            docker stop mern-client || true
            docker rm mern-client || true

            docker stop mern-server || true
            docker rm mern-server || true

            echo "Starting backend..."

            docker run -d \
                --name mern-server \
                -p 5000:5000 \
                "$ECR_REGISTRY/mern-server:latest"

            echo "Starting frontend..."

            docker run -d \
                --name mern-client \
                -p 3000:80 \
                "$ECR_REGISTRY/mern-client:latest"

            echo "Deployment completed."

            docker ps
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
