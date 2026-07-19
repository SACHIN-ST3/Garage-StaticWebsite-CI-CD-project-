pipeline {
    agent any

    environment {

        AWS_REGION = 'us-east-1c'

        IMAGE_NAME = 'static-website'
        IMAGE_TAG  = 'latest'
        IMAGE_URI  = 'public.ecr.aws/z4f5h9h6/static-website:latest'

        CLUSTER_NAME = 'my_eks_static_web'

        DEPLOYMENT_NAME = 'static-website'
        NAMESPACE = 'default'
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Login to Amazon ECR Public') {
            steps {
                sh '''
                    aws ecr-public get-login-password \
                    --region ${AWS_REGION} | \
                    docker login \
                    --username AWS \
                    --password-stdin public.ecr.aws
                '''
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_URI}
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                    docker push ${IMAGE_URI}
                '''
            }
        }

        stage('Configure kubectl') {
            steps {
                sh '''
                    aws eks update-kubeconfig \
                    --region ${AWS_REGION} \
                    --name ${CLUSTER_NAME}
                '''
            }
        }

        stage('Deploy to Amazon EKS') {
            steps {
                sh '''
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                '''
            }
        }

        stage('Rolling Update') {
            steps {
                sh '''
                    kubectl rollout restart deployment/${DEPLOYMENT_NAME} -n ${NAMESPACE}

                    kubectl rollout status deployment/${DEPLOYMENT_NAME} -n ${NAMESPACE}
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    echo "========== Nodes =========="
                    kubectl get nodes

                    echo "========== Pods =========="
                    kubectl get pods -o wide

                    echo "========== Deployment =========="
                    kubectl get deployment

                    echo "========== Services =========="
                    kubectl get svc
                '''
            }
        }

    }

    post {

        success {

            echo '======================================='
            echo 'Build Successful'
            echo 'Docker Image Built Successfully'
            echo 'Image Pushed to Amazon ECR'
            echo 'Application Deployed to Amazon EKS'
            echo 'Rolling Update Completed'
            echo '======================================='

            sh '''
                kubectl get svc
            '''
        }

        failure {

            echo '======================================='
            echo 'Pipeline Failed'
            echo 'Please Check Jenkins Console Output'
            echo '======================================='
        }
    }
}
