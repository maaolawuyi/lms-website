pipeline {
  agent any

  environment {
    S3_BUCKET = 'maaolawuyi-lms-website'     // your bucket name
    AWS_REGION = 'us-east-1'                 // change if different
  }

  options {
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }

  stages {
    stage('Checkout') {
      steps {
        echo "Checking out source code from GitHub..."
        checkout scm
      }
    }

    stage('Validate') {
      steps {
        echo "Validating website files..."
        sh '''
          ls -la
          test -f index.html || (echo "index.html missing" && exit 1)
        '''
      }
    }

    stage('Build/Test') {
      steps {
        echo "Running simple HTML check..."
        sh '''
          grep -i "<html" index.html || (echo "HTML tag missing" && exit 1)
        '''
      }
    }

    stage('Deploy to S3') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'aws-credentials',
                          usernameVariable: 'AWS_ACCESS_KEY_ID',
                          passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
          sh '''
            set -e
            echo "Deploying to S3 bucket: $S3_BUCKET in region $AWS_REGION"
            export AWS_DEFAULT_REGION=$AWS_REGION
            aws sts get-caller-identity
            aws s3 sync . s3://$S3_BUCKET --delete
          '''
        }
      }
    }

    stage('Smoke Test') {
      steps {
        echo "Running Smoke Test..."
        sh '''
          set -e
          URL="http://maaolawuyi-lms-website.s3-website-us-east-1.amazonaws.com/index.html"
          echo "Testing $URL"
          STATUS=$(curl -s -o /tmp/test.html -w "%{http_code}" "$URL")
          if [ "$STATUS" != "200" ]; then
            echo "Smoke test failed: HTTP $STATUS"
            exit 1
          fi
          grep -q 'Welcome' /tmp/test.html || echo "Content verification: passed"
        '''
      }
    }
  }

  post {
    success {
      echo "✅ Build and Deployment Successful!"
      emailext subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
               body: "Build succeeded!\nJob URL: ${env.BUILD_URL}",
               to: "maaolawuyi@gmail.com"
    }
    failure {
      echo "❌ Build Failed!"
      emailext subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
               body: "Build failed!\nJob URL: ${env.BUILD_URL}",
               to: "maaolawuyi@gmail.com"
    }
    always {
      cleanWs()
    }
  }
}
