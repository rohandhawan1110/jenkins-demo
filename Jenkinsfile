pipeline {
    agent any

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'prod'],
            description: 'Choose deployment environment'
        )
    }

    environment {
        AWS_REGION = 'ap-southeast-2'
        ECR_REGISTRY = '470914319097.dkr.ecr.ap-southeast-2.amazonaws.com'
        ECR_REPOSITORY = 'jenkins-demo'
    }

    stages {

        stage('Show Parameters') {
            steps {
                echo "Selected environment: ${params.ENVIRONMENT}"
                echo "Build number: ${BUILD_NUMBER}"
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Test stage passed"'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                      -t jenkins-demo:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Run Test Container') {
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

        stage('Cleanup Test Container') {
            steps {
                sh 'docker rm -f jenkins-demo-test || true'
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

        stage('Deploy to Dev') {

            when {
                expression {
                    params.ENVIRONMENT == 'dev'
                }
            }

            steps {
                sh '''
                    docker pull \
                      ${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}

                    docker rm -f jenkins-demo-dev || true

                    docker run -d \
                      --name jenkins-demo-dev \
                      -p 5001:5000 \
                      ${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}

                    sleep 3

                    curl -f http://localhost:5001
                '''
            }
        }

        stage('Production Approval') {

            when {
                expression {
                    params.ENVIRONMENT == 'prod'
                }
            }

            steps {
                input(
                    message: "Deploy build ${BUILD_NUMBER} to production?",
                    ok: 'Deploy'
                )
            }
        }

        stage('Deploy to Prod') {

            when {
                expression {
                    params.ENVIRONMENT == 'prod'
                }
            }

            steps {
                sh '''
                    docker pull \
                      ${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}

                    docker rm -f jenkins-demo-prod || true

                    docker run -d \
                      --name jenkins-demo-prod \
                      -p 5002:5000 \
                      ${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}

                    sleep 3

                    curl -f http://localhost:5002
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
