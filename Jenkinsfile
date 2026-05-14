pipeline {
    agent any

    environment {
        ACR_LOGIN_SERVER = 'dockernew.azurecr.io'
        IMAGE_NAME = 'jenkins-java-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Ajayrichard/Jenkinsandjava.git'
            }
        }

        stage('Build Application') {
            steps {
                sh '''
                mvn clean package
                '''
            }
        }

        stage('Docker Login to ACR') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'azure-acr-creds',
                        usernameVariable: 'ACR_USERNAME',
                        passwordVariable: 'ACR_PASSWORD'
                    )
                ]) {

                    sh '''
                    echo $ACR_PASSWORD | docker login $ACR_LOGIN_SERVER \
                    --username $ACR_USERNAME \
                    --password-stdin
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG .

                docker tag \
                $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG \
                $ACR_LOGIN_SERVER/$IMAGE_NAME:latest
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                docker push $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG
                docker push $ACR_LOGIN_SERVER/$IMAGE_NAME:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
