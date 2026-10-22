pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-cicd-app .'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh 'docker rm -f devops-app || true'
            }
        }

        stage('Run Application') {
            steps {
                sh 'docker run -d --name devops-app -p 8081:80 devops-cicd-app'
            }
        }

    }

    post {

        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed!'
        }

    }
}
