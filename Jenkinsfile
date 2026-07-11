pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = 'YOUR_ECR_REPOSITORY_NAME'
        AWS_ACCOUNT_ID = 'YOUR_AWS_ACCOUNT_ID'
        IMAGE_TAG = "${BUILD_NUMBER}"

        IMAGE_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}"

        DEPLOY_SERVER = "ubuntu@YOUR_EC2_PUBLIC_IP"
        CONTAINER_NAME = "garage-website"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_URI} ."
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh """
                aws ecr get-login-password --region ${AWS_REGION} | \
                docker login \
                --username AWS \
                --password-stdin \
                ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                """
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push ${IMAGE_URI}"
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: ['nginx-ssh']) {

                    sh """
                    ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} '
                        aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login \
                        --username AWS \
                        --password-stdin \
                        ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com

                        docker pull ${IMAGE_URI}

                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true

                        docker run -d \
                            --name ${CONTAINER_NAME} \
                            --restart always \
                            -p 80:80 \
                            ${IMAGE_URI}
                    '
                    """
                }
            }
        }
    }

    post {

        success {
            echo 'Deployment Successful'
        }

        failure {
            echo 'Deployment Failed'
        }
    }
}
