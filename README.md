visudo

root    ALL=(ALL)       ALL
jenkins    ALL=(ALL)       NOPASSWD: ALL
ec2-user    ALL=(ALL)       NOPASSWD: ALL



https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html


sudo chmod +x ./kubectl

mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH



# sudo mv kubectl /usr/local/bin/

aws configure              –for credentials 



create secrete text credential AWS_Access_Key

create secrete text credential AWS_Secret_Key


pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_Access_Key')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_Secret_Key')
        AWS_DEFAULT_REGION = 'ap-south-1'
    }

     stages {
        stage('GitClone')
		{
            
            steps {
				   git branch: 'main', url: 'https://gitlab.com/kvpramasamy/sat-kube-deploy.git'

            }
        }
    
	 stage('Deploy Kubernates') 
	 {
		steps{
			script {
               sh 'aws eks --region ap-south-1 update-kubeconfig --name devcluster'
               sh 'kubectl apply -f ns.yaml'
			 // sh 'kubectl apply -f pvc.yaml'
			 // sh 'kubectl apply -f mongodb.yaml'
		   	  //sh 'kubectl apply -f newIngressctrl.yaml'
		   	  sh 'kubectl delete -f newIngressctrl.yaml'
			 // sh 'kubectl apply -f webcon.yaml'
			 sh 'kubectl delete -f webcon.yaml'
			  }      
          }
     }

     	
        
        
		 
    }
}




# in jenkins, create credentials of aws access and secret key with ##secret text.






