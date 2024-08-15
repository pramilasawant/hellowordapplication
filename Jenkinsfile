pipeline {
    agent any
    tools {
        maven 'maven'  // Use the correct Maven name from Jenkins Global Tool Configuration
        jdk 'JDK 17'   // Use the correct JDK name from Jenkins Global Tool Configuration
    }
    environment {
        SONARQUBE_SERVER = 'SonarQube'  // This should match the name given during the SonarQube server configuration
        SONARQUBE_TOKEN = credentials('sonar_token')  // Use Jenkins credentials ID for the SonarQube token
        JAVA_HOME = "${tool 'JDK 17'}"  // Set JAVA_HOME
        PATH = "${JAVA_HOME}/bin:${env.PATH}"  // Add JAVA_HOME to PATH
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/pramilasawant/hellowordapplication.git', branch: 'main'
            }
        }
        
        stage('Build') {
            steps {
                dir('hellowordapplication') { // Change to the correct directory if needed
                    sh 'mvn clean install'
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                dir('hellowordapplication') {
                    script {
                        withSonarQubeEnv(SONARQUBE_SERVER) { // Ensure this matches your SonarQube server name
                           withCredentials([string(credentialsId: 'sonar_token', variable: 'SONAR_TOKEN')]) {
                            mvn clean install sonar:sonar

                        }
                        }     
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Check the quality gate status
                    timeout(time: 1, unit: 'HOURS') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
    }
     
    post {
        success {
            echo 'Build and SonarQube analysis succeeded.'
            slackSend(channel: '#your-channel', color: 'good', message: "Pipeline succeeded: ${currentBuild.currentResult}")
        }
        failure {
            echo 'Build or SonarQube analysis failed.'
            slackSend(channel: '#your-channel', color: 'danger', message: "Pipeline failed at stage: ${currentBuild.currentResult}")
        }
    }
}
