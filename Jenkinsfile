pipeline {
   agent { label 'neelapc2' }

	
  environment {
    DOCKERHUB_CREDENTIALS = credentials('reddymnk')
    ANSIBLE_PRIVATE_KEY = credentials('ansible_key')
    //REMOTE_SERVER = '192.168.1.11'
    REMOTE_USER = 'neela1' 	  	  
  }
	
  // Fetch code from GitHub
	
  stages {
    stage('checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/reddymnk/Industry-Grade-Project'

      }
    }
	  
   // Build Java application
	  
    stage('Maven Build') {
      steps {
        sh 'sudo /opt/maven/bin/mvn clean install'
      }
	    
     // Post building archive Java application
	    
      post {
        success {
          archiveArtifacts artifacts: '**/target/*.war'
        }
      }
    }
	  
  // Test Java application
	  
    stage('Maven Test') {
      steps {
        sh 'sudo /opt/maven/bin/mvn test'
      }
    }
	  
   // Build docker image in Jenkins
	  
    stage('Build Docker Image') {

      steps {
        sh 'sudo docker build -t reddymnk/devops:latest .'
        //sh 'sudo docker tag abctechnologies reddymnk/devops:latest'
      }
    }
	  
   // Login to DockerHub before pushing docker Image
	  
    stage('Login to DockerHub') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u    $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
	  
   // Push image to DockerHub registry
	  
    stage('Push Image to dockerHUb') {
      steps {
        sh 'sudo docker push reddymnk/devops:latest'
      }
      post {
        always {
          sh 'sudo ocker logout'
        }
      }

    }
	  
   // Pull docker image from DockerHub and run in neelapc2 instance 
	  
   stage('Deploy to K8 cluster') {
        steps {
         // Run the Ansible playbook locally on the Jenkins machine
                sh  'ansible-playbook -i /var/lib/jenkins/hosts /var/lib/jenkins/playbook1.yaml --key-file "/var/lib/jenkins/ansible_key"'
	   }
	}
  }
}
