pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "320674390565.dkr.ecr.us-east-1.amazonaws.com/zomato"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/udi345/Zomato.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t zomato-app:latest .'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | docker login \
                    --username AWS --password-stdin $ECR_REPO
                    '''
                }
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                docker tag zomato-app:latest $ECR_REPO:latest
                docker push $ECR_REPO:latest
                '''
            }
        }
    }
}

