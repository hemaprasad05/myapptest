node{
   stage('SCM Checkout'){
     git 'https://github.com/hemaprasad05/myapptest.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven01', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven01', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Imager'){
   sh 'docker build -t hemaprasad05/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerpass', variable: 'dockerPassword')]) {
   sh "docker login -u hemaprasad05 -p ${dockerPassword}"
    }
   sh 'docker push hemaprasad05/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin 65.0.11.168:8085"
   sh "docker tag hemaprasad05/myweb:0.0.2 65.0.11.168:8085/prasad:1.0.0"
   sh 'docker push 65.0.11.168:8085/prasad:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest hemaprasad05/myweb:0.0.2' 
   }
}
}
