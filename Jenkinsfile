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
                sh 'exit 1'
            }
        }

        stage('Build') {
            steps {
                sh 'echo "Building application"'
            }
        }
    }
}
