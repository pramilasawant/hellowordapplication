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
                dir('hellowordapplication'){
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    sh 'mvn sonar:sonar -Dsonar.login=${SONARQUBE_TOKEN}'
                }
            }
        }
        }
        stage('Quality Gate') {
            steps {
                script {
                    def qualityGateStatus
                    try {
                        // Poll the SonarQube server to check the quality gate status
                        def response = sh(script: "curl -s -u ${SONARQUBE_TOKEN}: \"${SONARQUBE_SERVER}/api/qualitygates/project_status?projectKey=hellowordapplication\"", returnStdout: true).trim()
                        def json = readJSON(text: response)
                        qualityGateStatus = json.projectStatus.status
                        if (qualityGateStatus != 'OK') {
                            error "SonarQube Quality Gate failed: ${qualityGateStatus}"
                        }
                    } catch (Exception e) {
                        error "Failed to check SonarQube Quality Gate status: ${e.getMessage()}"
                    }
                }
            }
        }
    }
     
    post {
        success {
            echo 'Build and SonarQube analysis succeeded.'
        }
        failure {
            echo 'Build or SonarQube analysis failed.'
            slackSend(channel: '#your-channel', color: 'danger', message: "Pipeline failed at stage: ${currentBuild.currentResult}")
        }
    }
}
