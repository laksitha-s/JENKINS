pipeline {
    agent any

    environment {
        IMAGE_NAME = "yourdockerhubusername/my-app"
        REGISTRY = "docker.io"
        DOCKER_CREDENTIALS_ID = "docker-hub-credentials"
        GITHUB_CREDENTIALS_ID = "github-credentials"
        APP_DIR = "/opt/docker-kec"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: GITHUB_CREDENTIALS_ID, url: 'https://github.com/your-repo.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "cd $APP_DIR && docker build -t $IMAGE_NAME:latest ."
                }
            }
        }

        stage('Login to Docker Registry') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    }
                }
            }
        }

        stage('Push Image to Docker Registry') {
            steps {
                script {
                    sh "docker push $IMAGE_NAME:latest"
                }
            }
        }

        stage('Deploy using Docker Compose') {
            steps {
                script {
                    sh "cd $APP_DIR && docker-compose down && docker-compose up -d"
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully! '
        }
        failure {
            echo 'Pipeline failed! Check the logs for errors.'
        }
    }
}
