Project: CI/CD Pipeline on a Single EC2 Instance (Jenkins, GitHub, Maven, Tomcat)

This project demonstrates a streamlined CI/CD pipeline set up on a single AWS EC2 Ubuntu instance. It integrates GitHub for version control, Jenkins for automation, Maven for building the Java application, and Apache Tomcat for deploying the built artifacts. This setup minimizes infrastructure complexity, making it ideal for learning or developing small-scale applications. 

Table of contents
1. Project Overview
2. Prerequisites
3. EC2 Instance Setup
4. Java, Jenkins, Maven & Tomcat Installation
5. GitHub Integration with Jenkins
6. Creating a Jenkins Job for CI/CD
7. Accessing the Application

1. Project overview
This project showcases the following DevOps principles and technologies:

Continuous Integration: Jenkins automatically builds and tests code changes from GitHub.
Continuous Deployment: Automating the deployment of the application to a Tomcat server on the same EC2 instance using Jenkins.
Secure Access: AWS EC2 Instance Connect is used for secure and easy SSH access.
Dependency Management: Maven is used for building the Java application.
Application Server Setup: Apache Tomcat is configured to host the Java application.
Version Control: GitHub is used for source code management and integrating it with Jenkins. 

2. Prerequisites
Before starting this process, ensure the following are available:
AWS Account: With permissions to launch EC2 instances and create security groups.
GitHub Repository: Containing your Java application code.
Key Pair: Generated and downloaded when launching EC2 instances (still needed for initial setup and potential fallback access).

3. EC2 instance setup
Launch an EC2 Instance:
Log in to the AWS Management Console and go to the EC2 dashboard.
Click "Launch Instance".
AMI: Select "Ubuntu Server 20.04 LTS" or a compatible version.
Instance Type: Choose "t2.micro" (free tire) to accommodate both Jenkins and Tomcat.
Key Pair: Create or use an existing key pair (.pem file).
Security Groups: Create a security group to allow inbound traffic on:
SSH (port 22) from the IP ranges used by EC2 Instance Connect in your region.
TCP 8080 (for Jenkins and Tomcat) from your IP address or Anywhere.
Launch the instance.

5. Java, Jenkins, Maven & Tomcat installation
Connect to your EC2 instance: Use AWS EC2 Instance Connect for secure access by selecting your instance in the EC2 Dashboard and choosing "Connect". Select the "EC2 Instance Connect" tab, enter the username (e.g., ubuntu), and connect via the browser-based terminal.

Switch to root user with following command:
sudo su -

Install Java Development Kit (JDK): Run the following commands:
apt update -y
apt install openjdk-11-jdk -y


Install Jenkins: Use these commands to add the Jenkins repository, install, and start the service:

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null


systemctl start jenkins
systemctl status jenkins


Install Maven: Install Maven and verify the installation:

apt install maven
mvn -v


Install Tomcat: Download and extract the Tomcat archive, move it to /catologdev, and configure user and group ownership and permissions. See the referenced documents for the exact commands.
Configure Tomcat users: Edit /catologdev/tomcat/conf/tomcat-users.xml to add a user with manager-gui and manager-script roles for deployment. Replace yourusername and yourpassword with your desired credentials.
Change Tomcat port (Optional): If needed, modify the Tomcat port in /catologdev/tomcat/conf/server.xml to avoid conflicts with Jenkins.
Start Tomcat: Execute the startup script:

/catologdev/tomcat/bin/startup.sh


Access Jenkins and Tomcat UI: Access Jenkins at http://your-ec2-public-ip:8080. Unlock it with the initial password from /var/lib/jenkins/secrets/initialAdminPassword, install plugins, and create a user. Access Tomcat at http://your-ec2-public-ip:8080 (or the custom port). 

5. GitHub integration with Jenkins
Configure GitHub Webhooks: In your GitHub repository settings, go to Webhooks. Add a webhook with the Payload URL http://your-ec2-public-ip:8080/github-webhook/, set content type to application/json, and select "Just the push event". Save the webhook. 

6. Creating a Jenkins job for CI/CD
Install Jenkins Plugins: In Jenkins, go to Manage Jenkins -> Manage Plugins and install "Maven Integration plugin" and "Deploy to container Plugin".
Create a New Freestyle Project: In Jenkins, click "New Item", choose "Freestyle project", give it a name, and click "OK".
Configure Source Code Management: Select "Git", enter your GitHub repository URL, add credentials, and specify the branch (e.g., main).
Configure Build Triggers: Select "GitHub hook trigger for GITScm polling" to trigger builds on GitHub pushes.
Configure Build Steps: Add a build step to execute mvn clean install.
Configure Post-build Actions (Deployment): Use the "Deploy to container Plugin" to add a deployment action. Add the Tomcat credentials, specify the WAR file path (e.g., target/your-application.war), and set the Context Path (e.g., /your-application). Select the Tomcat container URL: http://localhost:8080 (or your custom port).
Save and Run: Save the job configuration and trigger a build manually or by pushing code to GitHub. 

7. Accessing the application
Your application should now be accessible at: http://your-ec2-public-ip:8080/your-application-context-root 









