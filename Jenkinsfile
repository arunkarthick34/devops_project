node('slave'){
   stage('SCM Checkout'){
     git 'https://github.com/arunkarthick34/devops_project.git'
   }
   stage('Compile-Package'){
	   
	   sh "mvn clean package"
	   sh 'mv target/myweb*.war target/newapp.war'
	   sh 'cp target/newapp.war /home/newapp.war'
	   
	   
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
	
 try {
        stage('Test') {
            sh 'echo "Fail!"; exit 1'
        }
        echo 'This will run only if successful'
    } catch (e) {
        echo 'This will run only if failed'

        // Since we're catching the exception in order to report on it,
        // we need to re-throw it, to ensure that the build is marked as failed
        throw e
    } finally {
        def currentResult = currentBuild.result ?: 'SUCCESS'
        if (currentResult == 'UNSTABLE') {
            echo 'This will run only if the run was marked as unstable'
        }

        def previousResult = currentBuild.getPreviousBuild()?.result
        if (previousResult != null && previousResult != currentResult) {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }

        echo 'This will always run'
    }
}


