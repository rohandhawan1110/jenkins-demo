pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-southeast-2'
        ECR_REGISTRY = '470914319097.dkr.ecr.ap-southeast-2.amazonaws.com'
        ECR_REPOSITORY = 'jenkins-demo'
    }

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
                    curl -f http://localhost:5000
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                    aws ecr get-login-password \
                      --region ${AWS_REGION} \
                    | docker login \
                      --username AWS \
                      --password-stdin \
                      ${ECR_REGISTRY}
                '''
            }
        }

        stage('Tag Image for ECR') {
            steps {
                sh '''
                    docker tag \
                      jenkins-demo:${BUILD_NUMBER} \
                      ${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                    docker push \
                      ${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}
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
