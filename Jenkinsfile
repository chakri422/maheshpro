pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                echo "Code pulled from GitHub"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-website .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 80:80 my-website'
            }
        }
    }
}
