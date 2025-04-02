# Steps to install Ngnix on ubuntu VM using Ansible

## Step 1.Make sure to provision two Virtual machine
   1.1.Install jenkins and Ansible in same server
   1.2 Installing ngnix using ansible in another server

## 1.1.Install jenkins 
Install Java (Jenkins Dependency)
Jenkins requires Java. Install OpenJDK:
- sudo apt update
- sudo apt install openjdk-17-jdk -y  # Use Java 11 or later
- java -version
### 1.2 Install Jenkins
- wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

- echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

- sudo apt update

- sudo apt install jenkins -y

### 1.3 Start and Enable Jenkins

- sudo systemctl start jenkins
- sudo systemctl enable jenkins
- sudo systemctl status jenkins

### 1.4 Configure Firewall for Jenkins

- sudo ufw allow 8080
- sudo ufw enable

### 1.5 Get Initial Jenkins Admin Password

- sudo cat /var/lib/jenkins/secrets/initialAdminPassword
- Copy the password and open Jenkins in a browser:

## step 2 Install ansible

### 2.1 SSH into your Jenkins server.

Install Ansible:

- sudo apt update
- sudo apt install -y ansible

Verify the installation:
- ansible --version

### 2.2 Update ansible config file

[kafka_vm]
nginx_vm ansible_host=10.0.2.5 
ansible_user=guest 
ansible_ssh_private_key_file=/var/lib/jenkins/.ssh/ansible_id_rsa

## step:3  Genrate SSH key
### 3.1 GitHub SSH Key → Used for Jenkins to fetch code from GitHub.
### 3.2 Ansible SSH Key → Used for Jenkins/Ansible to SSH into the target VM (nginx_vm).

### 3.1 GitHub SSH Key
- sudo su - jenkins
- ssh-keygen -t rsa -b 4096 -f /var/lib/jenkins/.ssh/github_id_rsa -N ""
    - Add the public key (github_id_rsa.pub) to GitHub:
    - Go to GitHub → Settings → SSH and GPG keys → New SSH Key
    - Copy and paste the contents of:
    - cat /var/lib/jenkins/.ssh/github_id_rsa.pub
- Update Jenkins Git credentials to use this key.
  
### 3.2 Ansible SSH Key (For Slave Node Login)

- ssh-keygen -t rsa -b 4096 -f /var/lib/jenkins/.ssh/ansible_id_rsa -N ""
  
  **Copy this key to nginx_vm (10.0.2.5):**
- ssh-copy-id -i /var/lib/jenkins/.ssh/ansible_id_rsa guest@10.0.2.5
  
 **Change the permissions of the rsa file**
- sudo su - jenkins
- sudo chown jenkins:jenkins /var/lib/jenkins/.ssh/ansible_id_rsa
- sudo chmod 700 /var/lib/jenkins/.ssh
- sudo chmod 600 /var/lib/jenkins/.ssh/ansible_id_rsa
- sudo chmod 644 /var/lib/jenkins/.ssh/ansible_id_rsa.pub
- sudo systemctl restart jenkins
  
**run this command from jenkins server** 
- sudo su - jenkins
- ssh -i /var/lib/jenkins/.ssh/ansible_id_rsa guest@10.0.2.5

## Step 4 configure new project in Jenkins

4.1 - New item--> select pipeline project -->save it
4.2 - Go to created project --> select configure --> paste the jenkinsfile
4.3 - make sure to add git ssh key in the below path
    - Manage jenkins --> Credentials --> add generated git ssh from the jenkins server(step 3.2)
    - cat /var/lib/jenkins/.ssh/github_id_rsa.pub

## Verification steps

- open the brower and search **http://10.0.2.5/**
- Jenkins file and ansible playbook can be found in this git project o





