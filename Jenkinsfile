pipeline {
    agent any

    stages {
        stage ('Code Checkout') {
            steps { 
                git url "https://github.com/premkdev/node-app.git", branch: "main"
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
                    // Variables
                    def AWS_ACCOUNT_ID = "060795907993"
                    def AWS_REGION = "us-east-1"
                    def ECR_REPO_NAME = "node-app-deploy"

                    // Login to ECR
                    sh """
                        aws ecr get-login-password --region ${us-east-1} \
                        | docker login --username AWS --password-stdin ${060795907993}.dkr.ecr.${us-east-1}.amazonaws.com
                    """

                    // Create repo if not exists (safe)
                    sh """
                        aws ecr describe-repositories --repository-names ${node-app-deploy} --region ${us-east-1} || \
                        aws ecr create-repository --repository-name ${node-app-deploy} --region ${us-east-1}
                    """

                    // Tag the image
                    sh """
                        docker tag node-app:latest ${060795907993}.dkr.ecr.${us-east-1}.amazonaws.com/${node-app-deploy}:latest
                    """

                    // Push image to ECR
                    sh """
                        docker push ${060795907993}.dkr.ecr.${us-east-1}.amazonaws.com/${node-app-deploy}:latest
                    """

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
