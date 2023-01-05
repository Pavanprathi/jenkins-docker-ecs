pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="893129602050"
        AWS_DEFAULT_REGION="ap-south-1" 
	CLUSTER_NAME="jenkins-docker-ecs"
	SERVICE_NAME="jenkins-docker-ecs"
	TASK_DEFINITION_NAME="jenkins-docker-ecs"
	DESIRED_COUNT="1"
        IMAGE_REPO_NAME="jenkins-docker-ecs"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	registryCredential = "IAM Credentials"
    }
   
    stages {             
    // Building Docker images
      stage('Building image') {
         steps{
          script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
      stage('Pushing to ECR') {
         steps{  
           script {
			docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) {
                    	dockerImage.push()
                	}
         }
        }
      }
      
      stage('Deploy') {
         steps{
            withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}

