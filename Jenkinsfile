pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                echo 'Running SonarQube analysis...'

                withSonarQubeEnv('SonarQube') {
                    sh '''
                        ${tool 'SonarScanner'}/bin/sonar-scanner \
                        -Dsonar.projectKey=devops-cicd-project \
                        -Dsonar.projectName="DevOps CI/CD Project" \
                        -Dsonar.sources=. \
                        -Dsonar.sourceEncoding=UTF-8 \
                        -Dsonar.exclusions=.git/**,Jenkinsfile,Dockerfile
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                sh '''
                    docker build -t devops-cicd-app:latest .
                '''
            }
        }

        stage('Trivy Security Scan') {
            steps {
                echo 'Scanning Docker image for HIGH and CRITICAL vulnerabilities...'

                sh '''
                    trivy image \
                    --severity HIGH,CRITICAL \
                    --exit-code 1 \
                    devops-cicd-app:latest
                '''
            }
        }

        stage('Stop Old Container') {
            steps {
                echo 'Stopping old application container...'

                sh '''
                    docker rm -f devops-app || true
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Starting new application container...'

                sh '''
                    docker run -d \
                    --name devops-app \
                    -p 8081:80 \
                    devops-cicd-app:latest
                '''
            }
        }

    }

    post {

        success {
            echo '========================================'
            echo ' CI/CD PIPELINE SUCCESSFUL 🚀'
            echo '========================================'
        }

        failure {
            echo '========================================'
            echo ' CI/CD PIPELINE FAILED ❌'
            echo 'Check the failed stage above.'
            echo '========================================'
        }

        always {
            echo 'Pipeline execution completed.'
        }
    }
}
