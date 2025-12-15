pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO_NAME = "ecr-node-app"
        ECR_URL = "public.ecr.aws/u1j2f8m8/ecr-node-app"
    }

    stages {

        stage('Code Checkout') {
            steps {
                git url: "https://github.com/premkdev/node-app.git", branch: "main"
                echo "Code has been cloned"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t node-app:latest ."
                echo "Docker image built successfully"
            }
        }

        stage('Push Image to ECR') {
            steps {
                script {
                    sh """
                      aws ecr-public get-login-password --region us-east-1 \
                    | docker login --username AWS --password-stdin public.ecr.aws
                    """
                  sh "docker build -t ecr-node-app ."
                  sh "docker tag ecr-node-app:latest public.ecr.aws/u1j2f8m8/ecr-node-app:latest"
                  sh "docker push public.ecr.aws/u1j2f8m8/ecr-node-app:latest"

                   echo "Image pushed to ECR successfully"
                }
           }
        }
     }
    post {
        always {
            script {
                def result = currentBuild.currentResult
                emailext(
                    attachLog: true,
                    subject: "Build ${result}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: """
                        <p><b>Build Finished</b></p>
                        <p>Job: ${env.JOB_NAME}</p>
                        <p>Build Number: ${env.BUILD_NUMBER}</p>
                        <p>Status: ${result}</p>
                        <p>URL: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                    """,
                    to: 'premkumarchinnaraji@gmail.com'
                )
            }
        }

        success {
            echo "Build successful!"
        }

        failure {
            echo "Build failed!"
        }
    }
}
