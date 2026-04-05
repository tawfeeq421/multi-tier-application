pipeline {
    agent any
	
	tools {
	   jdk 'JDK17'
	   maven 'maven9'
	   
	}
	environment {
	    VERSION = "1.0.${BUILD_NUMBER}
	    SLACK_SERVER = "#devopscicd"
	    DOCKER_CREDS = credentials('docker-hub')
	}
	stages{
	    stage('Checkout'){
		   steps{
		      git 'https://github.com/tawfeeq421/multi-tier-application.git'
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

