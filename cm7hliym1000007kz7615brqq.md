---
title: "Mastering the CICD DevOps Pipeline for Corporate Projects"
seoTitle: "Streamlining CI/CD DevOps for Corporate Projects"
seoDescription: "Master the CICD DevOps pipeline for corporate projects with infrastructure setup, Jenkins, Docker, and more in this comprehensive guide"
datePublished: Sun Feb 23 2025 12:19:28 GMT+0000 (Coordinated Universal Time)
cuid: cm7hliym1000007kz7615brqq
slug: mastering-the-cicd-devops-pipeline-for-corporate-projects
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740312995771/7ac322ac-5f3a-425c-955f-34b878251eb7.jpeg
tags: docker, aws, github, git, developer, devops, jenkins, ci-cd, devops-articles, jenkins-devops, devops-journey, cicd-pipelines, devopscommunity, jenkins-server

---

## **ULTIMATE CICD PIPELINE PROJECT 🚀**

### **PHASE 1: INFRASTRUCTURE SETUP 🛠️**

#### **1\. Creating 3 Ubuntu 24.04 VM Instances on AWS 🌐**

1. **Sign in to the AWS Management Console:**
    
    1. * Go to [AWS Management Console](https://aws.amazon.com/console/).
            
        
        * Sign in with your AWS account credentials.
            
        

2. **Navigate to EC2:**
    
    1. * Type "EC2" in the search bar or select "Services" &gt; "EC2" under the "Compute" section.
            

3. **Launch Instance:**
    
    1. * Click "Instances" in the EC2 dashboard sidebar.
            
        
        * Click the "Launch Instance" button.
            
        

4. **Choose an Amazon Machine Image (AMI):**
    
    1. * Select "Ubuntu" from the list of available AMIs.
            
        
        * Choose "Ubuntu Server 24.04 LTS".
            
        
        * Click "Select".
            
        

5. **Choose an Instance Type:**
    
    1. * Select an instance type (e.g., t2.micro for testing).
            
        
        * Click "Next: Configure Instance Details".
            
        

6. **Configure Instance Details:**
    
    1. * Configure optional settings or leave them as default.
            
        
        * Click "Next: Add Storage".
            
        

7. **Add Storage:**
    
    1. * Specify the root volume size (default is usually fine).
            
        
        * Click "Next: Add Tags".
            
        

8. **Add Tags:**
    
    1. * Optionally, add tags for better organization.
            
        
        * Click "Next: Configure Security Group".
            
        

9. **Configure Security Group:**
    
    1. * Allow SSH access (port 22) from your IP address.
            
        
        * Optionally, allow other ports (e.g., HTTP port 80, HTTPS port 443).
            
        
        * Click "Review and Launch".
            
        

10. **Review and Launch:**
    
    1. * Review the instance configuration.
            
        
        * Click "Launch".
            
        

11. **Select Key Pair:**
    
    1. * Select an existing key pair or create a new one.
            
        
        * Check the acknowledgment box.
            
        
        * Click "Launch Instances".
            
        

12. **Access Your Instance:**
    
    1. * Use an SSH client like MobaXterm:
            
            * Open MobaXterm and click "Session" &gt; "SSH".
                
            * Enter the public IP address of your instance.
                
            * Select "Specify username" and enter "ubuntu".
                
            * Under "Advanced SSH settings", select "Use private key" and browse to your key pair file (.pem).
                
            * Click "OK" to connect.
                

#### **2\. Install Docker on All 3 VMs 🐳**

**Step-by-Step Installation:**

1. **Install prerequisite packages:**
    
    1. ```yaml
        sudo apt-get update
        sudo apt-get install ca-certificates curl
        ```
        

2. **Download and add Docker's official GPG key:**
    
    1. ```yaml
        sudo install -m 0755 -d /etc/apt/keyrings
        sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
        sudo chmod a+r /etc/apt/keyrings/docker.asc
        ```
        

3. **Add Docker repository to Apt sources:**
    
    1. ```yaml
        echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
        ```
        

4. **Update package index:**
    
    1. ```yaml
        sudo apt-get update
        ```
        

5. **Install Docker packages:**
    
    1. ```yaml
        sudo apt-get install docker-ce docker-ce-cli containerd.io -y
        ```
        

6. **Grant permission to Docker socket (optional, for convenience):**
    
    1. ```yaml
        sudo chmod 666 /var/run/docker.sock
        ```
        

By following these steps, you should have successfully installed Docker on your Ubuntu system. You can now start using Docker to containerize and manage your applications.

---

### **Setting Up Jenkins on Ubuntu 🔧**

**Step-by-Step Installation:**

1. **Update the system:**
    
    1. ```yaml
        sudo apt-get update
        sudo apt-get upgrade -y
        ```
        

2. **Install Java (Jenkins requires Java):**
    
    1. ```yaml
        sudo apt install -y fontconfig openjdk-17-jre
        ```
        

3. **Add Jenkins repository key:**
    
    1. ```yaml
        sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
        ```
        

4. **Add Jenkins repository:**
    
    1. ```yaml
        echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
        ```
        

5. **Update the package index:**
    
    1. ```yaml
        sudo apt-get update
        ```
        

6. **Install Jenkins:**
    
    1. ```yaml
        sudo apt-get install -y jenkins
        ```
        

7. **Start and enable Jenkins:**
    
    1. ```yaml
        sudo systemctl start jenkins
        sudo systemctl enable jenkins
        ```
        

8. **Access Jenkins:**
    
    1. * Open a web browser and go to [http://your\_server\_ip\_or\_domain:8080](http://your_server_ip_or_domain:8080/).
            
        
        * You will see a page asking for the initial admin password. Retrieve it using:
            
            * ```yaml
                sudo cat /var/lib/jenkins/secrets/initialAdminPassword
                ```
                
        
        * Enter the password, install suggested plugins, and create your first admin user.
            
        

---

### **Installing Trivy on Jenkins Server 🔍**

**Step-by-Step Installation:**

1. **Install prerequisite packages:**
    
    1. ```yaml
        sudo apt-get install wget apt-transport-https gnupg lsb-release
        ```
        

2. **Add Trivy repository key:**
    
    1. ```yaml
        wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
        ```
        

3. **Add Trivy repository to sources:**
    
    1. ```yaml
        echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
        ```
        

4. **Update package index:**
    
    1. ```yaml
        sudo apt-get update
        ```
        

5. **Install Trivy:**
    
    1. ```yaml
        sudo apt-get install trivy
        ```
        

---

### **Setting Up Nexus Repository Manager Using Docker 📦**

**Step-by-Step Installation:**

1. **Pull the Nexus Docker image:**
    
    1. ```yaml
        sudo docker pull sonatype/nexus3
        ```
        

2. **Run the Nexus container:**
    
    1. ```yaml
        sudo docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3
        ```
        

3. **Access Nexus:**
    
    1. * Open a web browser and go to [http://your\_server\_ip\_or\_domain:8081](http://your_server_ip_or_domain:8081/).
            
        
        * The default username is `admin`. Retrieve the initial admin password from the log:
            
            * ```yaml
                sudo docker logs nexus 2>&1 | grep -i password
                ```
                
        
        * Complete the setup wizard.
            
        

---

### **Setting Up SonarQube Using Docker 📈**

**Step-by-Step Installation:**

1. **Create a network for SonarQube and PostgreSQL:**
    
    1. ```yaml
        sudo docker network create sonarnet
        ```
        

2. **Run PostgreSQL container:**
    
    1. ```yaml
        sudo docker run -d --name sonarqube_db --network sonarnet -e POSTGRES_USER=sonar -e POSTGRES_PASSWORD=sonar -e POSTGRES_DB=sonarqube -v postgresql:/var/lib/postgresql -v postgresql_data:/var/lib/postgresql/data postgres:latest
        ```
        

3. **Run SonarQube container:**
    
    1. ```yaml
        sudo docker run -d --name sonarqube --network sonarnet -p 9000:9000 -e sonar.jdbc.url=jdbc:postgresql://sonarqube_db:5432/sonarqube -e sonar.jdbc.username=sonar -e sonar.jdbc.password=sonar -v sonarqube_data:/opt/sonarqube/data -v sonarqube_extensions:/opt/sonarqube/extensions -v sonarqube_logs:/opt/sonarqube/logs sonarqube:latest
        ```
        

4. **Access SonarQube:**
    
    1. * Open a web browser and go to [http://your\_server\_ip\_or\_domain:9000](http://your_server_ip_or_domain:9000/).
            
        
        * The default username and password are both `admin`.
            
        

---

### **PHASE 2: SOURCE CODE SETUP 📝**

**Project Repo :** [HERE](https://github.com/jaiswaladi246/Mission.git)

#### **Creating a Private Repository on GitHub and Pushing Source Code Using Git Bash 🌟**

**Part 1: Create a Private Repository on GitHub**

1. **Sign in to GitHub:**
    
    1. * Go to [GitHub](https://github.com/).
            
        
        * Sign in with your GitHub account credentials.
            
        

2. **Create a New Repository:**
    
    1. * Click the "+" icon in the top-right corner and select "New repository".
            
        
        * Name your repository (e.g., `my-private-repo`).
            
        
        * Set the repository to "Private".
            
        
        * Optionally, add a description.
            
        
        * Click "Create repository".
            
        

**Part 2: Push Source Code Using Git Bash**

1. **Clone the existing repository:**
    
    1. ```yaml
        git clone https://github.com/jaiswaladi246/Mission.git
        cd Mission
        ```
        

2. **Add the private repository as a remote:**
    
    1. ```yaml
        git remote add private-repo https://github.com/your-username/my-private-repo.git
        ```
        

3. **Push the code to the private repository:**
    
    1. ```yaml
        git push private-repo main
        ```
        

---

### **Automating Docker Builds and Pushes in Jenkins Pipeline ⚙️**

**Pipeline Script for Jenkinsfile:**

```yaml
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/jaiswaladi246/Mission.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("my-image:${env.BUILD_ID}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials-id') {
                        dockerImage.push("${env.BUILD_ID}")
                    }
                }
            }
        }
    }
}
```

**Step-by-Step Guide:**

1. **Access Jenkins:**
    
    1. * Go to [http://your\_server\_ip\_or\_domain:8080](http://your_server_ip_or_domain:8080/).
            

2. **Create a New Pipeline Job:**
    
    1. * Click "New Item".
            
        
        * Enter a name (e.g., `Docker-Pipeline`).
            
        
        * Select "Pipeline" and click "OK".
            
        

3. **Configure the Pipeline:**
    
    1. * Under "Pipeline", select "Pipeline script".
            
        
        * Copy and paste the provided Jenkinsfile script.
            
        
        * Click "Save".
            
        

4. **Run the Pipeline:**
    
    1. * Click "Build Now" to start the pipeline.
            

---

**That's it!** You have now set up a complete CI/CD pipeline with Docker, Jenkins, Trivy, Nexus, and SonarQube on Ubuntu VMs. Congratulations on your progress and happy DevOps journey! 🚀