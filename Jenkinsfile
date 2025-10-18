pipeline {
  agent any
  environment {
    S3_BUCKET = 'maaolawuyi-lms-website'        // change to your bucket
    AWS_REGION = 'us-east-1'
  }
  options {
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Validate') {
      steps {
        echo "Validating site files..."
        sh 'ls -la site'
        // placeholder for basic checks
        sh 'test -f site/index.html || (echo "index.html missing" && false)'
      }
    }

    stage('Test (lint)') {
      steps {
        echo "Running simple HTML check..."
        // simple check: file contains <html> tag
        sh "grep -i '<html' site/index.html || echo 'No html tag found'"
      }
    }

    stage('Deploy to S3') {
      environment {
        // Jenkins credentials binding should inject these variables
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
      }
      steps {
        echo "Deploying to S3 bucket ${env.S3_BUCKET}"
        sh '''
          export AWS_DEFAULT_REGION=${AWS_REGION}
          aws s3 sync site/ s3://${S3_BUCKET}/ --delete
        '''
      }
    }
  }
  post {
    success {
      echo 'Pipeline succeeded'
      // notify via email or slack plugin, see setup below
    }
    failure {
      echo 'Pipeline failed'
    }
    always {
      echo "Cleaning workspace"
      cleanWs()
    }
  }
}
