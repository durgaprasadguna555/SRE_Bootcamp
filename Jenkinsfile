pipeline {
     agent any
     environment {
        registry = "gcr.io/manifest-pride-351714/my-webapp"
        //- update your credentials ID after creating credentials for connecting to GCR
        registryCredential = "My First Project"
        IMAGE_REPO_NAME="my-webapp"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "gcr.io/manifest-pride-351714/my-webapp/${IMAGE_REPO_NAME}"
        dockerImage = ""
        PROJECT_ID = 'manifest-pride-351714'
        CLUSTER_NAME = 'my-gke-cluster'
        LOCATION = 'us-central1-a'
        CREDENTIALS_ID = 
    }
    stages {

        stage ('checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/SRE_Bootcamp']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/durgaprasadguna555/SRE_Bootcamp.git']]])
            }
        }
       
        stage ('Build docker image') {
            steps {
                script {
                dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
        
        stage ('Gcloud auth') {
            steps {
                sh 'gcloud auth configure-docker'
            }
        }
      
       // Uploading Docker images into GCR
    stage('Pushing to GCR') {
     steps{  
         script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${REPOSITORY_URI}:${IMAGE_TAG }"
         }
        }
      }
      
      /////////////
      
     stage('Deploy to GKE') {
            steps{
                step([
                $class: 'KubernetesEngineBuilder',
                projectId: env.PROJECT_ID,
                clusterName: env.CLUSTER_NAME,
                location: env.LOCATION,
                manifestPattern: 'k8s.yaml',
                credentialsId: env.CREDENTIALS_ID,
                verifyDeployments: true])
            }
        }
    }

}
