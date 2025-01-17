pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = "381491848850"
        AWS_DEFAULT_REGION = "us-east-1"
        IMAGE_REPO_NAME = "majidjenkin"
        IMAGE_TAG = "latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
    stages {
        stage('Logging into AWS ECR') {
            steps {
                script {
                    sh """
                    aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com
                    """
                }
            }
        }
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[credentialsId: '',
                    url: 'https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo.git']]
                ])
            }
        }
        stage('Building Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_REPO_NAME}:${IMAGE_TAG}")
                }
            }
        }
        stage('Tagging Docker Image') {
            steps {
                script {
                    sh """
                    docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:${IMAGE_TAG}
                    """
                }
            }
        }
        stage('Pushing to AWS ECR') {
            steps {
                script {
                    sh """
                    docker push ${REPOSITORY_URI}:${IMAGE_TAG}
                    """
                }
            }
        }
    }
}