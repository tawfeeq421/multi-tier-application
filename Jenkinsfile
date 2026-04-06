pipeline {
    agent any
    tools{
        jdk "JDK17"
        maven "MAVEN3.9"
    }
    stages{
        stage('Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/tawfeeq421/multi-tier-application.git'
            }
        }
        stage('Build'){
            steps{
                sh 'mvn clean install -DskipTests'
            }
        }
    }
}