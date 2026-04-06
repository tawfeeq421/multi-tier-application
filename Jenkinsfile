COLOR_MAP = [
    'SUCCESS': 'good',
    "FAILURE": 'danger'
]

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
        stage('Unit Test'){
            steps{
                sh 'mvn test'
            }
        }
    }
    post{
        always{
            echo 'Slack Notification.'
            slackSend channel: '#devopscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n more info at: ${env.BUILD_URL}"
        }
    }
}