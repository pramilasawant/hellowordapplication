pipeline {
    agent any
    tools {
        maven 'maven'  // Use the correct Maven name from Jenkins Global Tool Configuration
        jdk 'JDK 17'   // Use the correct JDK name from Jenkins Global Tool Configuration
    }
    environment {
        SONARQUBE_SERVER = 'SonarQube'  // This should match the name given during the SonarQube server configuration
        JAVA_HOME = "${tool 'JDK 17'}"  // Set JAVA_HOME
        PATH = "${JAVA_HOME}/bin:${env.PATH}"  // Add JAVA_HOME to PATH
        SONAR_TOKEN = 'sonar_token'  // Add the SonarQube token
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
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube_Scanner/bin/sonar-scanner \
                            -Dsonar.projectKey=hellowordapplication \
                            -Dsonar.sources=src \
                            -Dsonar.java.binaries=target/classes \
                            -Dsonar.login=${SONARQUBE_TOKEN} \
                            -X
                    '''
                }
            }
        }
          stage('Quality Gate') {
            when {
                expression {
                    return currentBuild.result == null || currentBuild.result == 'SUCCESS'
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
