# Garage Static Website CI/CD Project

## Overview

This project demonstrates an end-to-end CI/CD pipeline for deploying a
Dockerized static website on AWS using GitHub, Jenkins, Docker, and
Amazon ECR.

## Architecture

``` text
GitHub -> Jenkins -> Docker Build -> Amazon ECR -> Docker Pull -> Docker Container (8083) -> Website
```

## Tech Stack

-   AWS EC2
-   Jenkins
-   Docker
-   Amazon ECR
-   Git & GitHub
-   AWS CLI
-   HTML/CSS/JavaScript
-   Nginx

## Project Structure

``` text
Garage-StaticWebsite-CI-CD-project-/
├── Dockerfile
├── Jenkinsfile
├── README.md
├── .gitignore
├── .dockerignore
├── index.html
├── css/
├── js/
├── images/
└── assets/
```

## Implementation Steps

1.  Launch an Ubuntu EC2 instance.
2.  Open ports 22, 8080, 8083 (and optionally 80/443).
3.  Install Git, Docker, Java, Jenkins, and AWS CLI.
4.  Add the Jenkins user to the Docker group.
5.  Push the project to GitHub.
6.  Build and test the Docker image locally.
7.  Push the image to Amazon ECR.
8.  Configure AWS authentication (IAM role recommended).
9.  Create a Jenkins Pipeline.
10. Configure a GitHub webhook.

## Docker Commands

``` bash
docker build -t static-website .
docker run -d --name static-website -p 8083:80 static-website
docker images
docker ps
```

## Jenkins Pipeline

-   Checkout Source
-   Build Docker Image
-   Login to Amazon ECR
-   Tag Image
-   Push Image
-   Pull Latest Image
-   Stop Old Container
-   Remove Old Container
-   Run New Container

## CI/CD Flow

``` text
Developer
  |
Git Push
  |
GitHub Webhook
  |
Jenkins
  |
Docker Build Image
  |
Amazon ECR
  |
Deploy Container
  |
Website Updated
```

## Skills Demonstrated

-   CI/CD
-   Jenkins
-   Docker
-   Amazon ECR
-   AWS EC2
-   Git & GitHub
-   Linux Administration

## Future Improvements

-   IAM Roles
-   SonarQube
-   Trivy
-   Prometheus & Grafana
-   Kubernetes
-   Terraform
-   HTTPS with Let's Encrypt
