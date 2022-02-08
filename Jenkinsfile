pipeline {
    agent any
   // environment {
		//DOCKERHUB_CREDENTIALS=credentials('docker-hub')
		//ipAddress = "ec2-13-232-53-107.ap-south-1.compute.amazonaws.com"
		//username = "ec2-user"
	//}
	
    stages {
    
        stage('Pulling Code form repo') {
            steps {
		git credentialsId: 'github-creds', branch: 'main',
		url: 'https://github.com/Mohit722/spring-mvc-crud.git'
                dir ('spring-mvc-crud'){
                sh "pwd"
               }
            }
        } 
              stage('Build'){
                steps{
                    dir ('spring-mvc-crud'){
                        sh "./mvnw package"
                    }
                }
            } 
	  stage ('Test'){
		  steps{
			  dir('spring-mvc-crud'){
				  sh './mvnw test'
			  }
		  }
		  post {
			  always{
				 junit 'spring-mvc-crud/target/surefire-reports/TEST-*.xml'

			  }
		  }
	  }
        stage ('Building a container'){
            steps{
                git credentialsId: 'github-creds', branch: 'master',
                url: 'https://github.com/Mohit722/spring-mvc-crud.git'
                sh "sudo docker build -f spring-mvc-crud-application-Pipeline/Dockerfile -t 3mmmm123/spring-mvc-crud:$BUILD_NUMBER ."
                
            }
        }        
            stage ('Pushing Image into Docker regestry'){
                steps{
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    sh "sudo docker push 3mmmm123/pet-clinic:$BUILD_NUMBER"
                }
            }
	
             stage ('Deplyoing'){
	     	     input {
		     message "Please enter remote host url and username"
		     ok "OK"
		     submitter "tiwarimohit722@gmail.com"
                     submitterParameter "whoIsSubmitter"
		     parameters{
                    string(name: 'ipAddress', defaultValue: '0.0.0.0', description: 'Ip address to login ')
                    string (name: 'username', defaultValue: 'admin', description: 'username to Login')
                    
                }

	     }
		     steps{				
		     sshagent (credentials: ['ssh-key']) {

   	  		   sh 'ssh -o StrictHostKeyChecking=no ${username}@${ipAddress} echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
		           sh 'ssh $username@$ipAddress sudo docker stop spring-mvc-crud || true'
			   sh 'ssh $username@$ipAddress sudo docker rm spring-mvc-crud || true'
		           sh 'ssh $username@$ipAddress sudo docker pull 3mmmm123/spring-mvc-crud:$BUILD_NUMBER'
			   sh 'ssh $username@$ipAddress sudo docker run -it -d --name spring-mvc-crud -p 8080:8080 3mmmm123/pet-clinic:$BUILD_NUMBER'
                   		}
		     }
		     post {
		     always {
		     echo "Application is running succsfully in this url http://${ipAddress}:8080"
		     }
                  }  
    }   
}


     post {
           failure{
                        mail body: " App Deployment failed Click here to get latest logs  ${env.BUILD_URL}/console",
                        subject: "Build Failed for ${env.JOB_NAME} ",
                        to: "tiwarimohit722@gmail.com"
                    }
                    success {
                        mail body: "App Deployment succsfull for full log click here ${env.BUILD_URL}/console",
                        subject: "Build Success for ${env.JOB_NAME}",
                        to: "tiwarimohit722@gmail.com"
                    }
		    
                }
