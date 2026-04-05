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
	}
}

