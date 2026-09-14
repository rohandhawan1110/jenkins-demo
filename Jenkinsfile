pipeline {
    agent any

    stages {

        stage('Test') {
            steps {
                sh 'echo "Test stage passed"'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jenkins-demo:${BUILD_NUMBER} .'
            }
        }
    }
}
