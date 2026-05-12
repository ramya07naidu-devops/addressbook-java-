pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = '896725786296.dkr.ecr.ap-south-1.amazonaws.com/jenkins-java-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Ajayrichard/Jenkinsandjava.git'
            }
        }

        stage('Build (Maven)') {
            steps {
                sh '''
                echo "Building Java application..."
                mvn clean package
                '''
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh '''
                echo "Logging into AWS ECR..."
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ECR_REPO
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "Building Docker image..."
                docker build -t $ECR_REPO:$IMAGE_TAG .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                echo "Pushing Docker image to ECR..."
                docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed '
        }
    }
}
