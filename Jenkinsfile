pipeline {
    agent any
    environment {
        // Change these to match your personal AWS account details
        AWS_ACCOUNT_ID = '598120810222'
        AWS_REGION     = 'ap-south-1' 
        REGISTRY       = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        
        // This securely pulls your credentials from the Jenkins provider
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('ECR Authentication') {
            steps {
                // The pipeline uses the loaded environment variables to run the login sequence smoothly
                sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${REGISTRY}"
            }
        }
        stage('Build & Push Microservices') {
            steps {
                script {
                    def services = [
                        'streaming-auth': './backend/authService',
                        'streaming-stream': './backend/streamingService',
                        'streaming-admin': './backend/adminService',
                        'streaming-chat': './backend/chatService',
                        'streaming-frontend': './frontend'
                    ]
                    
                    services.each { name, path ->
                        echo "Building image for ${name}..."
                        sh "docker build -t ${REGISTRY}/${name}:latest ${path}"
                        
                        echo "Pushing ${name} to Amazon ECR..."
                        sh "docker push ${REGISTRY}/${name}:latest"
                    }
                }
            }
        }
    }
}
