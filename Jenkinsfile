node{
   stage('SCM Checkout'){
     git 'https://github.com/Arshadihub/my-app.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Images'){
   sh 'docker build -t arshadcsinfo/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u arshadcsinfo -p ${dockerPassword}"
    }
   sh 'docker push arshadcsinfo/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 46.137.194.217:8083"
   sh "docker tag arshadcsinfo/myweb:0.0.2 46.137.194.217:8083/arshad:1.0.0"
   sh 'docker push 46.137.194.217:8083/arshad:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest arshadcsinfo/myweb:0.0.2' 
   }
}
}
