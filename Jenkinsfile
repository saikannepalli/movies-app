node{
     
    stage('SCM Checkout'){
        git credentialsId: 'GIT_CREDENTIALSS', url: 'https://github.com/saikannepalli/moviesapp.git', branch: 'master'
    }
	
    stage('This stage shows the user'){
	  sh "whoami"
		
	}
    
    stage('Build frontend Docker Image'){
	    sh "sudo docker build -t saikannepalli/frontend1:latest frontend1/"
    }
    stage('Build backend Docker image'){
	    sh "sudo docker build -t saikannepalli/backend:latest backend/"
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'sai-dockerpwd', variable: 'sample')]) {
          sh "sudo docker login -u saikannepalli -p ${sample}"
        }
        sh "sudo docker push saikannepalli/frontend1:latest"
      	sh "sudo docker push saikannepalli/backend:latest"
     }
     
	 
     stage("Deploy To Kuberates Cluster"){
        withCredentials([file(credentialsId: 'demo-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
         sh "gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}"
         sh "gcloud config set project mssdevops-284216"
         sh "gcloud config set compute/zone us-central1-c"
         sh "gcloud config set compute/region us-central1"
         sh "gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project mssdevops-284216"
         //sh "sed -i -e 's,image_to_be_deployed,'maniengg/spring-boot-mongo:${BUILD_ID}',g' springBootMongo.yml"
        
        sh " kubectl apply -f TeamKubernetes-master/frontend/deployment/frontend-deployment.yaml -n samplemovie"
         	  sh " kubectl apply -f TeamKubernetes-master/frontend/deployment/frontend-service.yaml -n samplemovie"
	
	/** Backend **/
	   sh " kubectl apply -f TeamKubernetes-master/backend/deployment/backend-deployment.yaml -n samplemovie"
	   sh " kubectl apply -f TeamKubernetes-master/backend/deployment/backend-service.yaml -n samplemovie"
      
	  /** database **/
			
          sh " kubectl apply -f TeamKubernetes-master/database/deployment/database-deployment.yaml -n samplemovie"	
	         sh " kubectl apply -f TeamKubernetes-master/database/deployment/database-service.yaml -n samplemovie"
        }
      }
}
