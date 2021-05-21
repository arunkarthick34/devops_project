node('slave'){
	try {
   stage('SCM Checkout'){
     git 'https://github.com/arunkarthick34/devops_project.git'
   }
   stage('Compile-Package'){
	   try{
	   sh "mvn clean package"
	   sh 'mv target/myweb*.war target/newapp.war'
	   sh 'cp target/newapp.war /home/newapp.war'
	   }
	   finally{
		   echo "success"
	   }
	   
	   
   } 
   stage('SonarQube Analysis') {
	      /*  def mvnHome =  tool name: 'maven', type: 'maven'*/
	        withSonarQubeEnv('sonar') { 
	          sh "/bin/mvn sonar:sonar"
	        }
	   
	    }
	    
   stage('Build Docker Imager'){
   sh 'docker build -t arunkarthick34/myweb:0.0.2 .'
   } 
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u arunkarthick34 -p ${dockerPassword}"
    }
   sh 'docker push arunkarthick34/myweb:0.0.2'
   }
	/*
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 65.2.121.249:8083"
   sh "docker tag saidamo/myweb:0.0.2 65.2.121.249:8083/damo:1.0.0"
   sh 'docker push 65.2.121.249:8083/damo:1.0.0'
   } */
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest arunkarthick34/myweb:0.0.2' 
   }
}
	
	}
	
       catch(error){
		echo "build failed"
	}
}


