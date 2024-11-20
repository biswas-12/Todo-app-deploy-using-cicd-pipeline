# Declarative CI/CD Pipeline for a Todo-App

This project demonstrates building a **declarative CI/CD pipeline** for a simple Todo-App. Using **GitHub**, **Docker**, **DockerHub**, **AWS EC2**, and **Jenkins**, this guide walks you through automating the process from code commit to deployment.

## 🎯 Project Overview

The goal of this project is to set up a CI/CD pipeline that automates the build, test, and deployment processes for a **Todo-App**. By the end of this guide, your app will automatically deploy on an EC2 instance whenever new code is pushed to GitHub.

---

## 🛠️ Tools and Technologies Used

### GitHub  
- Version control system for managing the codebase.

### Docker  
- Containerizes the application for consistency across environments.  
**Commands Used**:
- `docker build -t app-name .`: Builds a Docker image.
- `docker ps`: Lists running containers.
- `docker run -p 8000:8000 app-name`: Runs the containerized application on port 8000.

### DockerHub  
- Artifact repository for storing Docker images.  
**Commands Used**:
- `docker login`: Authenticate your Docker client with DockerHub.

### AWS EC2  
- Deployment server hosting the app.

### Jenkins  
- Automates the CI/CD pipeline using a declarative pipeline syntax.  
**Commands Used**:
- `service status jenkins`: Checks the status of the Jenkins service.

---

## ⚙️ Setup Steps

### **Step 1: Set Up EC2 Instance and Clone the App Repository**

1. Launch an EC2 instance and connect to it using SSH.
2. Clone the app repository:
   ```bash
   git clone https://github.com/your-repo/todo-app.git
   ```

---

### **Step 2: Install Docker and Configure Permissions**

1. Install Docker:
   ```bash
   sudo apt install docker.io
   ```
2. Test Docker installation:
   ```bash
   docker ps
   ```
3. Resolve "Permission Denied" errors:
   ```bash
   sudo usermod -aG docker $USER
   sudo reboot
   ```
4. Verify Docker works after reboot:
   ```bash
   docker ps
   ```

---

### **Step 3: Install and Configure Jenkins**

1. Install Jenkins:
   ```bash
   sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
   echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt-get update
   sudo apt-get install jenkins
   ```
2. Grant Jenkins access to Docker:
   ```bash
   sudo usermod -aG docker jenkins
   ```
3. Access Jenkins at `http://<public-ip>:8080`.  
   - Update EC2 security group inbound rules:
     - **Port Range**: 8080
     - **Source**: My IP
   - Save the changes.

---

### **Step 4: Log In to DockerHub from EC2**

Authenticate Docker with your DockerHub account:
```bash
docker login
```

---

### **Step 5: Configure the CI/CD Pipeline in Jenkins**

1. Retrieve Jenkins' admin password:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
2. Log in to Jenkins and create a new pipeline job.
3. Use the following **Groovy script** to automate:
   ```groovy
   pipeline {
       agent any
       stages {
           stage('Build') {
               steps {
                   echo 'Building Docker Image...'
                   sh 'docker build -t your-dockerhub-repo/todo-app .'
               }
           }
           stage('Push') {
               steps {
                   echo 'Pushing Docker Image to DockerHub...'
                   sh 'docker push your-dockerhub-repo/todo-app'
               }
           }
           stage('Deploy') {
               steps {
                   echo 'Deploying Application to EC2...'
                   sh 'ssh -i your-key.pem ec2-user@your-ec2-ip "docker pull your-dockerhub-repo/todo-app && docker run -d -p 8000:8000 your-dockerhub-repo/todo-app"'
               }
           }
       }
   }
   ```
4. Save and run the pipeline.

---

### **Step 6: Verify the App Deployment**

1. Access the app at `http://<public-ip>:8000`.
2. If inaccessible, update the EC2 security group inbound rules:
   - **Port Range**: 8000
   - **Source**: My IP
   - Save the changes.

---
