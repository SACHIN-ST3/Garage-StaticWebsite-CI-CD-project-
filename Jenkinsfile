pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"

        IMAGE_NAME = "static-website"
        IMAGE_TAG  = "latest"
        IMAGE_URI  = "public.ecr.aws/z4f5h9h6/static-website:latest"

        CLUSTER_NAME = "my-static-website-cluster"

        DEPLOYMENT_NAME = "static-website"
        SERVICE_NAME = "static-website-service"

        NAMESPACE = "default"
    }

    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Verify Environment') {
            steps {
                sh '''
                echo "========== Docker =========="
                docker --version

                echo "========== AWS =========="
                aws --version

                echo "========== kubectl =========="
                kubectl version --client

                echo "========== eksctl =========="
                eksctl version
                '''
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

                echo "========== Cluster =========="
                kubectl cluster-info

                echo "========== Nodes =========="
                kubectl get nodes
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
                kubectl rollout restart deployment/${DEPLOYMENT_NAME}

                kubectl rollout status deployment/${DEPLOYMENT_NAME}
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

                echo "========== Deployments =========="
                kubectl get deployments

                echo "========== ReplicaSets =========="
                kubectl get rs

                echo "========== Services =========="
                kubectl get svc

                echo "========== Describe Deployment =========="
                kubectl describe deployment ${DEPLOYMENT_NAME}
                '''
            }
        }
    }

    post {

        success {

            echo "=========================================="
            echo "CI/CD PIPELINE COMPLETED SUCCESSFULLY"
            echo "=========================================="

            sh '''
            echo "========== Final Services =========="
            kubectl get svc

            echo "========== External URL =========="
            kubectl get svc ${SERVICE_NAME}
            '''
        }

        failure {

            echo "=========================================="
            echo "PIPELINE FAILED"
            echo "=========================================="

            sh '''
            kubectl get pods --all-namespaces || true

            kubectl get deployments || true

            kubectl get svc || true
            '''
        }

        always {
            cleanWs()
        }
    }
}
