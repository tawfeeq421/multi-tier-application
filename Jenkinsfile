pipeline {
     agent any 

     tools {
	maven 'Maven'
	jdk 'jdk17'
     }
     
     environment {
	 APP_NAME = "myapp"
	 DOCKER_IMAGE = "tawfeeq421/java-app
	 VERSION = 1.0.${BUILD_NUMBBER}"
	 DOCKER_CREDS = 'docker-hub'
	 SONARQUBE_ENV = 'sonarqube-server'
	 SLACK_CHANNEL = '#devops-alerts'
     }

     stages{
	 stage('Chckout'){
	     steps{
	        git 'https://github.com/tawfeeq421/multi-tier-application.git'
	     }
	 }

	 stage ('Build') {
	     steps{
	        sh 'mvn install -DskipTets'

	     }
	 }

	 stage('Unit Test'){
	     steps{
	        sh "mvn test"
	     }
	 }

	 stage('SonarQube Analysis'){
	     environment {
		scannerHome = tool 'sonar6.2'
		PROJECT_KEY = "vprofile"
		PROJECT_NAME = "vprofile"
		VERSION = 1.0.${BUILD_NUMBER}
	     }
	     steps{
	        withSonarQubeEnv("${SONAR_SERVER}"){
		     sh """
		     ${scannerHome}/bin/sonar-scanner \
		     -Dsonar.projectKey=${PROJECT_KEY} \
		     -Dsonar.projectName=${PROJECT_NAME} \
		     -Dsonar.projectVersion=${VERSION}
		     -Dsonar.sources=src \
		     -Dsonar.java.binaries=target/classes \
		     -Dsonar.junit.reportPaths=target/surefire-reports \
		     -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
		     """
		}
	     }
	 }

	 stage('Quality Gate'){
	    steps{
	       timeout(time: 5, unit: 'MINUTES'){
	       	   waitForQualityGate abortPipeline: true
	       }
	    }
	 }
		
	 stage('OWASP Dependency Check'){
	     steps{
	         sh """
		 dependency-check.sh \
		 --project vprofile \
		 --scan . \
		 --format HTML \
		 --out dependency-check-report
		 """
	     }
	 }

	 stage('Trivy Files Scan'){
	     steps{
	         sh 'trivy fs .'
	     }
	 }

	 stage('Build Docker Image'){
	     steps{
	         sh "docker build -t $DOCKER_IMAGE:$VERSION .'

	     }
	 }

	 stage ('Trivy Image Scan'){
	     steps{
		 sh "trivy image $DOCKER_IMAGE:$VERSION"
	     }
	 }
	 stage('Push Docker Image') {
	     steps{
		 echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_REDS_USR --password-stdi
		 docker push $DOCKER_IMAGE:$VERSION
		
	     }
	 }

     }
}
