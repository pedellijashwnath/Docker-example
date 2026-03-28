pipeline {
    agent any
    environment {
        PATH = "/usr/local/bin:/opt/homebrew/bin:${env.PATH}"
    }
    stages {
        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/pedellijashwnath/Docker-example.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jashwanthram9848/html-demo:latest .'
            }
        }
        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'docker login -u $USERNAME -p $PASSWORD'
                    sh 'docker push jashwanthram9848/html-demo:latest'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
