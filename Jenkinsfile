currentBuild.displayName="movies-app-#"+currentBuild.number

pipeline {
    agent any
     stages{
        stage('Build Docker Image'){
            steps{
                //sh "sudo docker build . -t saikannepalli/movies-app:${BUILD_ID} "
		sh "sudo docker build . -t saikannepalli/frontend1:${BUILD_ID} "
	        sh "sudo docker build . -t saikannepalli/backend:${BUILD_ID} "
            }
        }
        stage('DockerHub Push'){
            steps{
               withCredentials([string(credentialsId: 'sai-dockerpwd', variable: 'sample')]) {
                    sh "sudo docker login -u saikannepalli -p ${sample}"
                  //  sh "sudo docker push srinivasareddy4218/movies-app:${BUILD_ID}"
		     sh "sudo docker push saikannepalli/frontend1:${BUILD_ID} "  
		    sh "sudo docker push saikannepalli/backend:${BUILD_ID} "
                }
            }
        }
	 
     stage("Deploy To Kuberates Cluster"){
	     steps{
        withCredentials([file(credentialsId: 'demo-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
         sh "gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}"
         sh "gcloud config set project mssdevops-284216"
         sh "gcloud config set compute/zone us-central1-c"
         sh "gcloud config set compute/region us-central1"
         sh "gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project mssdevops-284216"
         //sh "sed -i -e 's,image_to_be_deployed,'maniengg/spring-boot-mongo:${BUILD_ID}',g' springBootMongo.yml"
        
        sh " kubectl apply -f frontend/deployment/frontend-deployment.yaml -n samplemovie"
         	  sh " kubectl apply -f frontend/deployment/frontend-service.yaml -n samplemovie"
	
	/** Backend **/
	   sh " kubectl apply -f backend/deployment/backend-deployment.yaml -n samplemovie"
	   sh " kubectl apply -f backend/deployment/backend-service.yaml -n samplemovie"
      
	  /** database **/
			
          sh " kubectl apply -f database/deployment/database-deployment.yaml -n samplemovie"	
	         sh " kubectl apply -f database/deployment/database-service.yaml -n samplemovie"
        }
      }
     }
     }
}

