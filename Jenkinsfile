pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/opt/homebrew/bin:${env.PATH}"
        IMAGE_NAME = "jashwanthram9848/html-demo"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/pedellijashwnath/Docker-example.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker pull ${IMAGE_NAME}:latest || true

                docker build \
                  --cache-from=${IMAGE_NAME}:latest \
                  -t ${IMAGE_NAME}:${IMAGE_TAG} \
                  -t ${IMAGE_NAME}:latest .
                """
            }
        }

        stage('Push Image') {
            when {
                branch 'main'
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh """
                    echo "$PASSWORD" | docker login -u "$USERNAME" --password-stdin

                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${IMAGE_NAME}:latest

                    docker logout
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }
            steps {
                sh """
                kubectl set image deployment/html-demo \
                html-demo=${IMAGE_NAME}:${IMAGE_TAG} || \
                kubectl apply -f deployment.yaml
                """
            }
        }
    }

    post {
        always {
            sh "docker system prune -f || true"
        }
        success {
            echo "Build ${IMAGE_TAG} deployed successfully"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}
