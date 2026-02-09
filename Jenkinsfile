pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "320674390565.dkr.ecr.us-east-1.amazonaws.com/bookmyshow"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/udi345/Book-My-Show.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t bookmyshow:latest .'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $ECR_REPO
                    '''
                }
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                docker tag bookmyshow:latest $ECR_REPO:latest
                docker push $ECR_REPO:latest
                '''
            }
        }
    }
}
