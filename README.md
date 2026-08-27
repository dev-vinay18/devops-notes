Jenkins + SonarQube Setup and Pipeline
This guide explains how to integrate Jenkins with SonarQube for code analysis using EC2 instances.

1. Create EC2 Instances
Instance 1: Jenkins (instance type : t2.medium or more)
Instance 2: SonarQube (instance type : t2.medium or more)
2. Setup Jenkins EC2 Server
Install Java 17
sudo apt install openjdk-17-jdk -y
Install Jenkins
Follow official Jenkins documentation: 👉 Jenkins Installation Guide

Install Maven
sudo apt install maven -y
3. Setup SonarQube EC2 Server
Install Docker
sudo apt install docker.io -y
Run SonarQube
docker run -d --name sonarqube-custom -p 9000:9000 sonarqube:10.6-community
4. Access Jenkins & SonarQube
Jenkins: http://<jenkins-public-ip>:8080
SonarQube: http://<sonarqube-public-ip>:9000
Login SonarQube with:
Username: admin
Password: admin
Change the default password after first login.
5. Configure SonarQube
Create Webhook
Go to: Administration → Configuration → Webhooks → Create
Name: Sonar-webhook
URL: http://<jenkins-public-ip>:8080/sonarqube-webhook/
Create a Project
Go to Projects → Create Project → *Local Project
Project display Name: studentapp
Project key: studentapp
Main branch name: main then click Next
Select Use global settings
Generate token → Copy & save it
Select Maven as build tool → Copy the given command
6. Configure Jenkins
1. Install Plugin
Dashboard → Manage Jenkins → Plugins → Available Plugins
Install: SonarQube Scanner for Jenkins
2. Add Credentials
Dashboard → Manage Jenkins → Credentials → Global credentials (unrestricted)
Add new credential:
Kind: Secret Text
Secret: <SonarQube Token>
ID: sonar-token
3. Configure SonarQube Server
Dashboard → Manage Jenkins → System
Find SonarQube Server section
Enable environment variable
Add new SonarQube:
Name: Sonar-env
Server URL: http://<sonarqube-public-ip>:9000
Authentication Token: sonar-token
Save changes Note (optional): After configuring, restart the Jenkins server to ensure it operates smoothly. (http://:8080/restart)
Update the pom.xml with sonarqube dependancy
7. Create Jenkins Pipeline
Go to Dashboard → New Item → Pipeline
Paste the following pipeline code:
pipeline {
    agent any
    stages {
        stage('pull') {
            steps {
                git branch: 'main', url: 'https://github.com/Rohit-1920/EasyCRUD-Updated.git'
            }
        }
        stage('build') {
            steps {
                sh '''cd backend
                    mvn clean package -DskipTests'''
            }
        }
        stage('test') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonar-token', installationName: 'Sonar-env'){
                    sh '''cd backend
                        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \\
                        -Dsonar.projectKey=studentapp \\
                        -Dsonar.projectName=studentapp'''
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true, credentialsId: 'sonar-token'
                }
            }
        }
    }
}
8. Run the Pipeline
Build the job in Jenkins.
Check results in SonarQube Dashboard.
✅ Summary
Jenkins → CI/CD automation
SonarQube → Code quality & security analysis
Integration ensures code passes quality checks before deployment.
