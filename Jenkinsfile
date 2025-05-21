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

