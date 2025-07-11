Jenkins Lab Setup Guide
✅ Prerequisites
Ensure Docker is installed on your VM. Then verify the following:

bash
Copy
Edit
# Check Docker service status
systemctl status docker

# Check Docker version
docker --version
1. 🛠️ Jenkins Installation via Docker
Step 1: Pull Jenkins Docker Image
bash
Copy
Edit
docker pull jenkins/jenkins:lts
Step 2: Run Jenkins Container
bash
Copy
Edit
docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
Step 3: Get Jenkins Admin Password
bash
Copy
Edit
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
2. ⚙️ Create Freestyle Job to Run app.py
Step 1: Create the Job
Go to Jenkins Dashboard → “New Item”

Name it: run-python-app-freestyle

Select Freestyle Project → Click OK

Step 2: Configure Git
Source Code Management: Git

Repo URL:

bash
Copy
Edit
https://github.com/devops99669/my-python-app.git
Branch: main

Leave credentials blank if public.

Step 3: Add Build Step
Under Build → Add build step → Execute shell, paste:

bash
Copy
Edit
echo "✅ Running Python script..."
python3 app.py
Step 4: Save and Run
Click Save

Click Build Now

Check Console Output

Expected output:

bash
Copy
Edit
✅ Running Python script...
Hello from Git to test to run jenkins pipeline
3. 📧 Configure Jenkins Mail Server (Gmail)
Go to: Manage Jenkins → Configure System

Scroll to E-mail Notification and set:

SMTP server: smtp.gmail.com

Use SMTP Authentication: ✅

Username: your-email@gmail.com

Password: your-app-password

Use SSL: ✅

SMTP Port: 465

Reply-To Address: (optional)

Click Test Configuration

4. 📨 Post-Build Email Notification
Go to your job → Configure

Scroll to Post-build Actions

Add E-mail Notification

Configure:

Recipients: your-email@gmail.com

Triggers: ✅ Success, ✅ Unstable builds

5. 🔗 Jenkins Master-Agent Setup via SSH
Prerequisites
Jenkins Master (Docker or VM)

Separate Agent VM with:

SSH Access

Java Installed

Step 1: Prepare Agent Node
bash
Copy
Edit
sudo apt update
sudo apt install default-jre -y
sudo useradd -m -s /bin/bash jenkins
sudo passwd jenkins
Step 2: Set Up SSH Key
bash
Copy
Edit
ssh-keygen -t rsa -b 4096
ssh-copy-id jenkins@<agent_ip>
Step 3: Configure Jenkins Master
Go to: Manage Jenkins → Manage Nodes and Clouds → New Node

Name: agent1, select Permanent Agent

Set:

Executors: 1

Remote Root Directory: /home/jenkins

Launch Method: Launch agent via SSH

Host: <agent_ip>

Credentials: Add jenkins SSH credentials

Host Key Verification: Non-verifying (for lab/test only)

Step 4: Launch Agent
Click Save → Then click Launch Agent

Agent should be online and connected

6. 🚧 Sample Pipeline to Run on Agent
groovy
Copy
Edit
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
7. 📊 Summary Table
Component	Purpose
Jenkins Master	Controls jobs and UI
Jenkins Agent	Executes builds/jobs
SSH Setup	Secure communication (Master ↔ Agent)
Email Setup	Notifications via Gmail
Pipeline Targeting	Use labels to assign builds to agents
