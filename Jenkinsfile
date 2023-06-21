pipeline {
 agent any
 environment {
 AWS_ACCOUNT_ID="192182953222"
 AWS_DEFAULT_REGION="us-east-1" 
 IMAGE_REPO_NAME="jenkins-pipeline-build-demo"
 // PARAM_IMAGE_TAG ="${IMAGE_TAG}"
 // IMAGE_TAG="${GIT_COMMIT}"  
 SELECTED_IMAGE_TAG = "${IMAGE_TAG}:${BRANCH_NAME}"
 REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
 CLUSTER_NAME = "ecs-cluster"
 TASKDEF_NAME = "ecs_terraform_task_def"  
 SERVICE_NAME = "ecs_terraform_service"  
 }
 
 stages {
 
 stage('Cloning Git') {
 steps {
 checkout scmGit(branches: [[name: '${BRANCH_NAME}']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/sahilj713/multi-branch-cicd.git']])
 }
 }
 
 // Building Docker images
 stage('Building image') {
 steps{
 script {
 dockerImage = docker.build "${IMAGE_REPO_NAME}:${SELECTED_IMAGE_TAG}"  
 }
 }
 }
 
 
 }
 }