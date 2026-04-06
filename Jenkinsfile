def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
]

pipeline {
    agent any

    tools {
        jdk "JDK17"
        maven "MAVEN3.9"
    }

    environment {
        registryCredential = 'awscreds'
        appRegistry = "554903865932.dkr.ecr.ap-south-1.amazonaws.com/javaapp"
        vprofileRegistry = "554903865932.dkr.ecr.ap-south-1.amazonaws.com"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/tawfeeq421/multi-tier-application.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Checkstyle Analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage("Sonar Code Analysis") {
            environment {
                scannerHome = tool 'sonar'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh """
                    ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=vprofile \
                    -Dsonar.projectName=vprofile \
                    -Dsonar.projectVersion=1.0 \
                    -Dsonar.sources=src/ \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.junit.reportsPath=target/surefire-reports \
                    -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
                    """
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs . > trivyfs.txt'
            }
        }

        stage('Build App Image') {
            steps {
                script {
                    def dockerImage = docker.build("${appRegistry}:${BUILD_NUMBER}", "./Docker-files/app/")
                    sh "docker tag ${appRegistry}:${BUILD_NUMBER} ${appRegistry}:latest"
                }
            }
        }

        stage('Upload App Image') {
            steps {
                script {
                    withCredentials([[
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: 'awscreds'
                    ]]) {
                        sh """
                        aws ecr get-login-password --region ap-south-1 | \
                        docker login --username AWS --password-stdin ${vprofileRegistry}

                        docker push ${appRegistry}:${BUILD_NUMBER}
                        docker push ${appRegistry}:latest
                        """
                    }
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh "trivy image ${appRegistry}:${BUILD_NUMBER} > trivyimage.txt"
            }
        }

        stage('Remove Container Images') {
            steps {
                sh 'docker rmi -f $(docker images -a -q)'
            }
        }
    }

    post {
        always {
            echo 'Slack Notification.'
            slackSend channel: '#devopscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n more info at: ${env.BUILD_URL}"
        }
    }
}