pipeline {
    agent any
	
	tools {
	   jdk 'JDK17'
	   maven 'maven9'
	   
	}
	stages{
	    stage('Checkout'){
		   steps{
		      git branch: 'main', url: 'https://github.com/tawfeeq421/multi-tier-application.git'
		   }
		}
		
		stage('Build') {
		    steps{
			   sh 'mvn install -DskipTets'
			}
		}
		stage('Unit Test'){
		    steps{
			   sh 'mvn test'
			}
		}
		stage('Trivy Files Scan'){
		    steps{
			   sh 'trivy fs .'
			}
		}
		 stage("Sonar Code Analysis") {
        	     environment {
                         scannerHome = tool 'sonar6.2'
                     }
                     steps {
                        withSonarQubeEnv('sonar-server') {
                          sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                          -Dsonar.projectName=vprofile \
                          -Dsonar.projectVersion=1.0 \
                          -Dsonar.sources=src/ \
                          -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                          -Dsonar.junit.reportsPath=target/surefire-reports/ \
                          -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                          -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
             }
          }

	}
}

