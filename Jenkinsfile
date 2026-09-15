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

        stage('Run Container') {
            steps {
                sh '''
                    docker rm -f jenkins-demo-test || true

                    docker run -d \
                      --name jenkins-demo-test \
                      -p 5000:5000 \
                      jenkins-demo:${BUILD_NUMBER}
                '''
            }
        }

        stage('Verify Application') {
            steps {
                sh '''
                    sleep 3
                    curl -f http://localhost:9000
                '''
            }
        }
    }

    post {
        always {
            sh 'docker rm -f jenkins-demo-test || true'
        }
    }
}
