
 
Jenkins Lab Setup Guide
Once Docker is installed, verify the following processes:
Check Docker service status
systemctl status docker
Verify Docker version
docker –version
1.	Jenkins Installation via Docker

Step 1: Pull Jenkins Docker Image
docker pull jenkins/jenkins:lts

Step 2: Run Jenkins Container
docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

Step 3: Retrieve Initial Admin Password
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword

2. Freestyle Job to Run app.py

Step 1: Create the Job
1. Go to Jenkins Dashboard
2. Click “New Item”
3. Name it: run-python-app-freestyle
4. Select Freestyle project
5. Click OK

Step 2: Configure Source Code Management (Git)
1. Under Source Code Management, select: Git
2. Enter the repo URL: https://github.com/devops99669/my-python-app.git
3. Leave credentials blank if public
4. Branch: main

Step 3: Add Build Step
1. Scroll to Build → Click Add build step → Choose Execute shell
2. Paste:
echo "✅ Running Python script..."
python3 app.py

Step 4: Save and Build
1. Click Save
2. Click Build Now
3. View Console Output

Expected Output:
✅ Running Python script...
Hello from Git to test to run jenkins pipeline

3. Configure Jenkins Mail Server

1. Navigate to: Manage Jenkins → Configure System
2. Scroll to E-mail Notification
3. Configure as below:
   - SMTP server: smtp.gmail.com
   - Use SMTP Authentication: ✅
   - Username: your-email@gmail.com
   - Password: your-app-password
   - Use SSL: ✅
   - SMTP Port: 465
   - Reply-To: (optional)
4. Click Test configuration

4. Post-Build Email Notification

1. Go to your Freestyle job → Configure
2. Scroll to Post-build Actions
3. Click Add post-build action → E-mail Notification
4. Set:
   - Recipients: your-email@gmail.com
   - Triggers:
     - "Send e-mail for every unstable build"
     - "Success"

5. Jenkins Master-Agent Setup (SSH)

Prerequisites:
- Jenkins Master (Docker or VM)
- Separate Agent VM with SSH access
- Java installed on Agent
- SSH access enabled

Step 1: Prepare Agent Node
sudo apt update
sudo apt install default-jre -y
sudo useradd -m -s /bin/bash jenkins
sudo passwd jenkins

Step 2: Setup SSH Key
ssh-keygen -t rsa -b 4096
ssh-copy-id jenkins@<agent_ip>

Step 3: Configure Jenkins Master
1. Go to: Manage Jenkins → Manage Nodes and Clouds → New Node
2. Name: agent1 → Select: Permanent Agent
3. Settings:
   - Executors: 1
   - Remote root directory: /home/jenkins
   - Launch method: Launch agent via SSH
   - Host: <agent_ip>
   - Credentials: Add SSH of jenkins user
   - Host Key Verification: Non-verifying (for test only)

Step 4: Launch Agent
- Click Save → Then Launch Agent
- Verify agent status is online

6. Sample Pipeline to Run on Agent

pipeline {
    agent { label 'agent1' }
    stages {
        stage('Build') {
            steps {
                sh 'echo Building on agent'
            }
        }
    }
}

7. Summary Table

Component         | Purpose
------------------|--------------------------------------
Jenkins Master    | Controls jobs and UI
Jenkins Agent     | Executes jobs
SSH Setup         | Secure connection master ↔ agent
Email Setup       | Notifications via Gmail
Pipeline Targeting| Use labels to assign jobs to agents

 

 
 

 

