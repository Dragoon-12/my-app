node{
   stage('SCM Checkout'){
     git 'https://github.com/damodaranj/my-app.git'
   }
   stage('maven-buildstage'){

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
   stage('Build Docker Image'){
   sh 'docker build -t dragoon12/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u dragoon12 -p ${dockerPassword}"
    }
   sh 'docker push dragoon12/myweb:0.0.2'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.232.44.232:8085"
   sh "docker tag dragoon12/myweb:0.0.2 13.232.44.232:8085/damo:1.0.0"
   sh 'docker push 13.232.44.232:8085/damo:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest dragoon12/myweb:0.0.2' 
   }
}
}
