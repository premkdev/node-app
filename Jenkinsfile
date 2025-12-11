pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = "060795907993"
        AWS_REGION = "us-east-1"
        ECR_REPO_NAME = "ecr-node-app"
        ECR_URL = "public.ecr.aws/u1j2f8m8/ecr-node-app"
    }

    stages {
        stage ('Code Checkout') {
            steps { 
                git url: "https://github.com/premkdev/node-app.git", branch: "main"
                echo "Code has been cloned"
            }
        }
        stage ('Build and test') {
            steps {
                sh "docker build -t node-app ."
                sh "docker run -d -p 8000:80 node-app"
                echo "docker build successfully"
            }
        }
        stage ('Img push to ECR') {
            steps {
                script {
                    // Login to ECR
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} \
                        | docker login --username AWS --password-stdin ${ECR_URL}
                    """
                    // Tag image
                    sh "docker tag node-app:latest ${ECR_URL}/${ECR_REPO_NAME}:latest"

                    // Push image
                    sh "docker push ${ECR_URL}/${ECR_REPO_NAME}:latest"

                    echo "Image pushed to ECR successfully"

                }

            }
        }
        stage ('Deploy') {
            steps {
                sh "docker-compose down && docker-compose up -d"
                echo "Deployed successfully"

            }
        }

    }
}
