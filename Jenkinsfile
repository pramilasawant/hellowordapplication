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
                script {
                    // Use the SonarQube Scanner plugin's built-in environment
                    withSonarQubeEnv(SONARQUBE_SERVER) { // Replace 'SonarQube' with your SonarQube server name
                        sh 'mvn sonar:sonar -Dsonar.login=${SONARQUBE_TOKEN} -Dsonar.host.url=http://your-sonarqube-server -Dsonar.projectKey=my-project-key'
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
            slackSend(channel: '#your-channel', color: '
