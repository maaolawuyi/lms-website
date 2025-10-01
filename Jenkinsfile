pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/maaolawuyi/lms-website.git'
            }
        }
        stage('Build') {
            steps {
                echo 'No build needed for static site'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing pipeline setup...'
            }
        }
    }
}
