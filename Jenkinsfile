pipeline {
    agent {
        docker {
            image 'node:latest'
            args '-u root' // Ensure root access for Docker container
        }
    }
    
    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        LAMBDA_FUNCTION_NAME = 'nodelambdafunction'
        GIT_REPO_URL = 'https://github.com/vivekraina2023/nodelambdafunction.git'
        TEMP_DIR = '/tmp/lambda_deploy'
        S3_BUCKET_NAME = 'vr01-nodelambdafunction'
        S3_KEY = 'lambda_function.zip'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: GIT_REPO_URL
            }
        }
        
        stage('Package Lambda Function') {
            steps {
                script {
                    sh 'npm install' // Assuming your Node.js Lambda function requires npm dependencies
                    sh 'mkdir -p $TEMP_DIR'
                    sh 'zip -r $TEMP_DIR/lambda_function.zip ./*'
                }
            }
        }
        
        stage('Upload to S3') {
            steps {
                script {
                    withAWS(region: AWS_DEFAULT_REGION, credentials: 'aws_credentials_id_vivekraina2023') {
                        sh "aws s3 cp $TEMP_DIR/lambda_function.zip s3://$S3_BUCKET_NAME/$S3_KEY"
                    }
                }
            }
        }
        
        stage('Deploy Lambda Function') {
            steps {
                script {
                    withAWS(region: AWS_DEFAULT_REGION, credentials: 'aws_credentials_id') {
                        sh "aws lambda update-function-code --function-name $LAMBDA_FUNCTION_NAME --s3-bucket $S3_BUCKET_NAME --s3-key $S3_KEY"
                    }
                }
            }
        }
    }
    
    post {
        always {
            sh 'rm -rf $TEMP_DIR'
        }
    }
}
