pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Code checked out from Git'
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Running test stage"'
            }
        }

        stage('Build') {
            steps {
                sh 'echo "Building application"'
            }
        }
    }
}
