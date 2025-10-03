pipeline {
    agent any
    triggers {
        // 👇 This makes Jenkins run whenever GitHub sends a webhook push event
        githubPush()
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/maaolawuyi/lms-website.git'
            }
        }
        stage('Build') {
            steps {
                echo 'No build needed for static website'
            }
        }
        stage('Deploy to S3') {
            steps {
                // 👇 Deploy everything in the repo to your S3 bucket
                sh 'aws s3 sync . s3://lms-website-project --delete'
            }
        }
    }
}
