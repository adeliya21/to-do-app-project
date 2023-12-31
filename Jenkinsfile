pipeline {
    agent any

    environment {
        ECR_REGISTRY = "502964439914.dkr.ecr.us-east-1.amazonaws.com"
        APP_REPO_NAME= "techtorial-repo/to-do-app"
    }

    stages {
        stage("Run app on Docker"){
            agent{
                docker{
                    image 'node:12-alpine'
                }
            }
            steps{
                withEnv(["HOME=${env.WORKSPACE}"]) {
                    sh 'yarn install --production'
                    sh 'npm install'
                }   
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build --force-rm -t "$ECR_REGISTRY/$APP_REPO_NAME:latest" .'
                sh 'docker image ls'
            }
        }

        stage('Push Image to ECR Repo') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin "$ECR_REGISTRY"'
                sh 'docker push "$ECR_REGISTRY/$APP_REPO_NAME:latest"'
            }
        }

        stage('Deploy') {
            steps {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin "$ECR_REGISTRY"'
                sh 'docker pull "$ECR_REGISTRY/$APP_REPO_NAME:latest"'

                sh 'docker ps -q --filter "name=to-do" | grep -q . && docker stop to-do && docker rm -f to-do'
                sh 'docker run --name to-do -dp 80:3000 "$ECR_REGISTRY/$APP_REPO_NAME:latest"'
            }
        }

    }

    post {
        always {
            echo 'Deleting all local images'
            sh 'docker image prune -af'
        }
    }

}