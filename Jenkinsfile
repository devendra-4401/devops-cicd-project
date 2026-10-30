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
                script {
                    echo 'Running SonarQube analysis...'

                    def scannerHome = tool 'SonarScanner'

                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=devops-cicd-project \
                            -Dsonar.projectName='DevOps CI/CD Project' \
                            -Dsonar.sources=. \
                            -Dsonar.sourceEncoding=UTF-8 \
                            -Dsonar.exclusions='.git/**,Jenkinsfile,Dockerfile'
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t devops-cicd-app:latest .'
            }
        }

        stage('Trivy Security Scan') {
            steps {
                echo 'Scanning Docker image for vulnerabilities...'

                sh '''
                    trivy image \
                    --severity HIGH,CRITICAL \
                    --exit-code 1 \
                    --no-progress \
                    devops-cicd-app:latest
                '''
            }
        }

        stage('Stop Old Container') {
            steps {
                echo 'Stopping old container...'
                sh 'docker rm -f devops-app || true'
            }
        }

        stage('Deploy Application') {
            steps {
                echo 'Deploying application...'
                sh 'docker run -d --name devops-app -p 8081:80 devops-cicd-app:latest'
            }
        }
    }

    post {

        success {
            echo '''
========================================
 CI/CD PIPELINE SUCCESSFUL ✅
========================================
'''
        }

        failure {
            echo '''
========================================
 CI/CD PIPELINE FAILED ❌
========================================
 Check the failed stage above.
'''
        }
    }
}
