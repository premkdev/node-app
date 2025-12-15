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
                   steps {
                    sh """
                       aws ecr get-login-password --region ${AWS_REGION} \
                       | docker login --username AWS --password-stdin ${ECR_URL}
                  """
                   sh "docker tag node-app:latest ${ECR_URL}/${ECR_REPO_NAME}:latest"
                   sh "docker push ${ECR_URL}/${ECR_REPO_NAME}:latest"

                   echo "Image pushed to ECR successfully"
                }
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
