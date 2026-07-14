**Real-world CI/CD pipeline**. Since you intend to showcase it on GitHub and use it in interviews, the README should explain not only *how* to run it but also the overall architecture and workflow.

---

# **GitHub Repository Structure**

Garage-StaticWebsite-CI-CD-project-/  
│  
├── Dockerfile  
├── Jenkinsfile  
├── README.md  
├── .gitignore  
├── .dockerignore  
│  
├── index.html  
├── css/  
├── js/  
├── images/  
├── fonts/  
└── assets/


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

---

# **Project Architecture**

               \+----------------------+  
                |      GitHub Repo     |  
                \+----------+-----------+  
                           |  
                     Git Push / Commit  
                           |  
                           ▼  
                \+----------------------+  
                |       Jenkins        |  
                | Pipeline from SCM    |  
                \+----------+-----------+  
                           |  
          Checkout Source Code  
                           |  
                           ▼  
                \+----------------------+  
                | Docker Build Image   |  
                \+----------+-----------+  
                           |  
                           ▼  
                \+----------------------+  
                | Amazon ECR Repository|  
                \+----------+-----------+  
                           |  
                 Pull Latest Docker Image  
                           |  
                           ▼  
                \+----------------------+  
                | Docker Container     |  
                | Port 8083            |  
                \+----------+-----------+  
                           |  
                           ▼  
                   Static Website

---

``` text
GitHub -> Jenkins -> Docker Build -> Amazon ECR -> Docker Pull -> Docker Container (8083) -> Website
```


# **Project Workflow**

## **Phase 1 — AWS Infrastructure**

### **Step 1**

Create an EC2 Ubuntu instance.

Example:

Ubuntu 24.04/26.04 LTS

t2.micro

Security Group

22

8080

8083

80

443

---

### **Step 2**

Connect to EC2

ssh \-i key.pem ubuntu@EC2\_PUBLIC\_IP

---

### **Step 3**

Update Server

sudo apt update  
sudo apt upgrade \-y

---

# **Phase 2 — Install Tools**

Install

* Git  
* Docker  
* Java  
* Jenkins  
* AWS CLI

Verify

git \--version

docker \--version

java \-version

aws \--version

---

# **Phase 3 — Configure Docker**

Start Docker

sudo systemctl enable docker  
sudo systemctl start docker

Allow Jenkins

sudo usermod \-aG docker jenkins

sudo systemctl restart jenkins

Verify

sudo su \- jenkins

docker ps

---

# **Phase 4 — Jenkins**

Open

http://EC2\_PUBLIC\_IP:8080

Unlock Jenkins

Install Suggested Plugins

Create Admin User

---

# **Phase 5 — GitHub**

Create Repository

Garage-StaticWebsite-CI-CD-project-

Push

git init

git add .

git commit \-m "Initial Commit"

git branch \-M main

git remote add origin URL

git push \-u origin main

---

# **Phase 6 — Docker**

Create

Dockerfile

Build

docker build \-t static-website .

Verify

docker images

Run

docker run \-d \\  
\--name static-website \\  
\-p 8083:80 \\  
static-website

Open

http://EC2\_PUBLIC\_IP:8083

---

# **Phase 7 — Amazon ECR**

Create Repository

Example

static-website

Login

Private ECR

aws ecr get-login-password

Public ECR

aws ecr-public get-login-password

Tag

docker tag

Push

docker push

Verify the image appears in Amazon ECR.

---

# **Phase 8 — AWS Credentials**

Recommended

Attach IAM Role

Permissions

AmazonEC2ContainerRegistryPowerUser

Alternative

aws configure

Verify

aws sts get-caller-identity

---

# **Phase 9 — Jenkins Pipeline**

Create Pipeline Job

Pipeline From SCM

Repository URL

Branch

main

Script Path

Jenkinsfile

---

# **Phase 10 — GitHub Webhook**

Repository

↓

Settings

↓

Webhooks

↓

Add Webhook

Payload

http://EC2\_PUBLIC\_IP:8080/github-webhook/

Content

application/json

Events

Push

---

# **Phase 11 — CI/CD Flow**

Developer

↓

Git Commit

↓

Git Push

↓

GitHub

↓

Webhook

↓

Jenkins

↓

Checkout Code

↓

Docker Build

↓

Docker Tag

↓

Docker Push

↓

Docker Pull

↓

Remove Old Container

↓

Run New Container

↓

Website Updated

---

# **Jenkins Pipeline Stages**

Build Docker Image

↓

Login Amazon ECR

↓

Tag Docker Image

↓

Push Docker Image

↓

Pull Latest Image

↓

Stop Existing Container

↓

Remove Existing Container

↓

Run New Container

---

# **Commands Used**

Git

git init

git add .

git commit

git push

Docker

docker build

docker images

docker ps

docker stop

docker rm

docker run

docker tag

docker push

docker pull

AWS

aws configure

aws sts get-caller-identity

aws ecr create-repository

aws ecr get-login-password

Jenkins

systemctl restart jenkins

---

# **Technologies Used**

* AWS EC2  
* Amazon ECR  
* Jenkins  
* Docker  
* Git  
* GitHub  
* AWS CLI  
* Linux  
* Nginx (inside the Docker image)  
* HTML  
* CSS  
* JavaScript

---

# **Skills Demonstrated**

* Continuous Integration  
* Continuous Deployment  
* Docker Containerization  
* AWS EC2 Administration  
* Amazon ECR  
* Jenkins Pipeline  
* Git & GitHub  
* Linux Administration  
* AWS CLI  
* Docker Image Management  
* DevOps Automation

---

# **Learning Outcomes**

* Built an end-to-end CI/CD pipeline.  
* Automated Docker image creation and deployment.  
* Integrated GitHub with Jenkins using webhooks.  
* Published Docker images to Amazon ECR.  
* Deployed a containerized static website on AWS EC2.  
* Configured Jenkins to perform automated deployments after every push to the repository.

---

