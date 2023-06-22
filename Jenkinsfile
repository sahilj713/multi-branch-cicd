//def buildUser = currentBuild.rawBuild.getCause(hudson.model.Cause$UserIdCause).getUserName()

pipeline {
 agent any
 environment {
 AWS_ACCOUNT_ID="192182953222"
 AWS_DEFAULT_REGION="us-east-1" 
 IMAGE_REPO_NAME="jenkins-pipeline-build-demo"
 // PARAM_IMAGE_TAG ="${IMAGE_TAG}"
 // IMAGE_TAG="${GIT_COMMIT}"  
 SELECTED_IMAGE_TAG = "${IMAGE_TAG}-${BRANCH_NAME}"
 BRANCH = "${BRANCH_NAME}" 
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
//  stage('Building image') {
//  steps{
//  script {
//  dockerImage = docker.build "${IMAGE_REPO_NAME}:${SELECTED_IMAGE_TAG}"  
//  }
//  }
//  }
  
//  stage('Logging into AWS ECR') {
//  steps {
//  script {
//  sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
//  }
//  }
//  }
  
// // Uploading Docker images into AWS ECR
//  stage('Pushing to ECR') {
//  steps{ 
//  script {
//  sh "docker tag ${IMAGE_REPO_NAME}:${SELECTED_IMAGE_TAG} ${REPOSITORY_URI}:$SELECTED_IMAGE_TAG"
//  sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${SELECTED_IMAGE_TAG}"
//  }
//  }
//  }

 stage('manual-approval'){
  when {
        expression {
          BRANCH == 'prod' || BRANCH == 'uat'
        }
      }
      steps {
        echo 'Deploying...'
        input message: 'Do you want to deploy to production? (y/n)', submitter:'sahil'
      }
 } 

 // stage('Ask for approval') {
 //      steps {
 //        // sh "env"
 //        input message: 'Do you approve?', ok:'proceed' ,submitter: 'Sarvesh05k'
 //       // BUILD_TRIGGER_BY = currentBuild.getBuildCauses()[0].shortDescription + " / " + currentBuild.getBuildCauses()[0].userId
 //       // echo "BUILD_TRIGGER_BY: ${BUILD_TRIGGER_BY}"
 //       // script{
 //       // def buildUser = input message:'who are you?'
 //       // echo "${buildUser}"
 //        // echo ${env.submitterParameter}
 //      // }
 //    }
 // }
    // stage('Check approval') {
    //   steps {
    //     script {
    //     // def buildUser = currentBuild.rawBuild.getCause(hudson.model.Cause$UserIdCause).getUserName()
    //     if (buildUser == submitter){
    //      sh "env"
    //     }
    //     }
    //   }
    // } 
 
 stage('deploy to ecs') {
  steps{
  script {
  sh "aws ecs describe-task-definition --task-definition ${ TASKDEF_NAME } > task-def.json"
  sh "jq .taskDefinition task-def.json > taskdefinition.json"
  sh "jq 'del(.taskDefinitionArn)' taskdefinition.json | jq 'del(.revision)' | jq 'del(.status)' | jq 'del(.requiresAttributes)' | jq 'del(.compatibilities)' | jq 'del(.registeredAt)'| jq 'del(.registeredBy)' > container-definition.json"
  sh "jq '.containerDefinitions[0].image = \"${REPOSITORY_URI}:${SELECTED_IMAGE_TAG}\"' container-definition.json > temp-taskdef.json"  
  sh "aws ecs register-task-definition --cli-input-json file://container-definition.json"  
  sh "aws ecs update-service --cluster  ${ CLUSTER_NAME } --service  ${ SERVICE_NAME } --task-definition  ${ TASKDEF_NAME }"
    }
   }
  }   
 
 
 }
 }
