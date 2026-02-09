pipeline {
agent any

environment {
    AWS_REGION = "us-east-1"
    ACCOUNT_ID = "320674390565"
    ECR_REPO = "bookmyshow"
    IMAGE_TAG = "latest"
    ECR_URI = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
}

stages {

    stage('Checkout Code') {
        steps {
            git branch: 'main',
            credentialsId: 'github-creds',
            url: 'https://github.com/YOUR_USERNAME/YOUR_REPO.git'
        }
    }

    stage('Build Docker Image') {
        steps {
            sh 'docker build -t $ECR_REPO:$IMAGE_TAG .'
        }
    }

    stage('Login to AWS ECR') {
        steps {
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }
    }

    stage('Tag Image') {
        steps {
            sh 'docker tag $ECR_REPO:$IMAGE_TAG $ECR_URI:$IMAGE_TAG'
        }
    }

    stage('Push Image to ECR') {
        steps {
            sh 'docker push $ECR_URI:$IMAGE_TAG'
        }
    }

    stage('Deploy to STAGING') {
        steps {
            sh '''
            kubectl apply -n staging -f Kubernetes/deployment.yaml
            kubectl apply -n staging -f Kubernetes/service.yaml
            '''
        }
    }

    stage('Promote to PRODUCTION') {
        steps {
            sh '''
            kubectl apply -n production -f Kubernetes/deployment.yaml
            kubectl apply -n production -f Kubernetes/service.yaml
            '''
        }
    }
}

}
