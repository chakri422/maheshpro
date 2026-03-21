pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ACCOUNT_ID = credentials('aws-account-id')   // store in Jenkins creds
        ECR_REPO = 'maheshimg'
        IMAGE_TAG = "v1.${BUILD_NUMBER}"   // dynamic tag (better than latest)
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/chakri422/maheshpro.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-website:${IMAGE_TAG} .'
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                docker tag my-website:${IMAGE_TAG} \
                $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:${IMAGE_TAG}
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-cred'
                ]]) {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin \
                    $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                    '''
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                docker push $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        always {
            echo "Cleaning up Docker images..."
            sh 'docker system prune -f'
        }
        success {
            echo "Pipeline executed successfully 🚀"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}
