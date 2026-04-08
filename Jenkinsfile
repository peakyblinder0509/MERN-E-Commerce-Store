pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        S3_BUCKET = "krishnagiri-bargur"
        CLOUDFRONT_DISTRIBUTION_ID = "E35FULF7FODTGJ"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'pat-creds',
                    url: 'https://github.com/peakyblinder0509/MERN-E-Commerce-Store.git'
            }
        }

        stage('Check Node Version') {
            steps {
                dir('frontend'){
                sh '''
                    echo "Node version:"
                    node -v
                    echo "NPM version:"
                    npm -v
                '''
            }
        }
        }
        stage('Install Dependencies') {
            steps {
                dir('frontend'){
                sh 'npm install --legacy-peer-deps'
            }
        }
        }  
        stage('Build') {
            steps {
                dir('frontend'){
                sh 'npm run build'
            }
        }
        }

        stage('Verify Build Output') {
            steps {
                dir('frontend'){
                sh '''
                    echo "Checking build folder..."
                    ls -la frontend/build || ls -la frontend/dist || echo "No build folder found!"
                '''
            }
        }
        }
        stage('Deploy to S3') {
            steps {
                withAWS(credentials: 'shana-cred', region: "${AWS_REGION}") {
                    sh '''
                        aws s3 sync frontend/dist/ s3://${S3_BUCKET}/ --delete
                    '''
                }
            }
        }

        stage('CloudFront Invalidation') {
            steps {
                withAWS(credentials: 'shana-cred', region: "${AWS_REGION}") {
                    sh '''
                        aws cloudfront create-invalidation \
                        --distribution-id ${CLOUDFRONT_DISTRIBUTION_ID} \
                        --paths "/*"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Frontend deployed successfully"
        }
        failure {
            echo "❌ Frontend deployment failed"
        }
    }
}
