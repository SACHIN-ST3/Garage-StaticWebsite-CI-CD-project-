pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'

        IMAGE_NAME = 'static-website'
        IMAGE_URI  = 'public.ecr.aws/z4f5h9h6/static-website:latest'

        CONTAINER_NAME = 'static-website'
        HOST_PORT = '8083'
        CONTAINER_PORT = '80'
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Login to Amazon ECR Public') {
            steps {
                sh '''
                aws ecr-public get-login-password --region ${AWS_REGION} | \
                docker login --username AWS --password-stdin public.ecr.aws
                '''
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                docker tag ${IMAGE_NAME}:latest ${IMAGE_URI}
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                docker push ${IMAGE_URI}
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker pull ${IMAGE_URI}

                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true

                docker run -d \
                    --name ${CONTAINER_NAME} \
                    --restart always \
                    -p ${HOST_PORT}:${CONTAINER_PORT} \
                    ${IMAGE_URI}
                '''
            }
        }
    }

    post {
        success {
            echo '========================================='
            echo 'Deployment Successful'
            echo 'Website: http://54.234.94.246:8083'
            echo '========================================='
        }

        failure {
            echo '========================================='
            echo 'Deployment Failed'
            echo '========================================='
        }
    }
}
