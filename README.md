This File is Related to Hosting Web Application Using Jenkins& Docker in Ec2 Instance
using Jenkins & Docker webhosting

✅ Project Goal
You want to:
1.Launch a web app (e.g., Node.js or Flask)
2.Host it on an EC2 instance using Docker
3.Use Jenkins to automate the build and deployment

STEP 1: Launch an EC2 Instance on AWS
	i)Go to AWS Console → EC2 → Launch Instance
Choose:
 	Name: jenkins-docker-server
	AMI: Ubuntu 22.04 LTS
	Instance Type: t2.medium
	Key Pair: Select or create new
ii)Security Group: Allow these ports:
	Port 22 → SSH
	Port 8080 → Jenkins
	Port 3000 → Web app (or your app port)
iii)Click Launch and wait for the instance to be ready

STEP 2: Connect to EC2 Instance and Install Required Tools
	i)Connect via SSH:
	ii)Install Docker
		sudo apt update
		sudo apt install docker.io -y
		sudo usermod -aG docker ubuntu 
	Logout&Login
	iii) Install Jenkins 
		sudo apt install openjdk-17-jdk -y 			#Java

	curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

	echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

		sudo apt update
		sudo apt install jenkins -y
		sudo systemctl start jenkins
		sudo systemctl enable jenkins
STEP 3: Access Jenkins in Browser
	i)Open http://<your-ec2-public-ip>:8080
	ii)Get the admin password:
	   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
	iii)Follow setup steps:
	    Install Suggested Plugins
	    Create an Admin User
STEP 4: Give Jenkins Docker Access
		sudo usermod -aG docker jenkins
		sudo systemctl restart jenkins
		Logout&Login

STEP 5: Create Sample Web App and Push to GitHub
	A. Create app locally or on EC2:
	1.index.js
		const express = require('express');
		const app = express();
		const PORT = 3000;
		app.get('/', (req, res) => res.send('Hello from Docker + Jenkins + EC2!'));
		app.listen(PORT, () => console.log(`Server on port ${PORT}`));

	2.package.json
		{
  "name": "my-web-app",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
	3.Dockerfile
		FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["npm", "start"]

B. Push to GitHub
	Create GitHub repo: my-web-app	
	Push your code:
	git init
	git remote add origin https://github.com/YOUR_USERNAME/my-web-app.git
	git add .
	git commit -m "Initial commit"
	git push -u origin master
STEP 6: Add Jenkinsfile to GitHub Repo
		pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/THIRU-GIT1997/my-web-app.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-web-app .'
            }
        }
        stage('Run Docker Container') {
            steps {
                sh 'docker stop my-web-container || true'
                sh 'docker rm my-web-container || true'
                sh 'docker run -d -p 3000:3000 --name my-web-container my-web-app'
            }
        }
    }
}
git add Jenkinsfile
git commit -m "Add Jenkinsfile"
git push

STEP 7: Create Jenkins Pipeline Job
		In Jenkins → New Item
		Choose Pipeline, name it my-web-app
		In Pipeline → Definition, choose:
		Pipeline from SCM
		SCM: Git
		Repo: https://github.com/THIRU-GIT1997/my-web-app.git
		Branch: master or main
		Click Save
		Click Build Now
  
STEP 8: Access Your App
		Open your app in a browser:
			http://<your-ec2-public-ip>:3000
		You should see:
			Hello from Docker + Jenkins + EC2!
