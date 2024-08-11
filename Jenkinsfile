pipeline {
    agent any
    tools {
        maven 'maven'  // Use the correct Maven name from Jenkins
        jdk 'JDK 11'      // Use the correct JDK name from Jenkins
    }
    environment {
        SONARQUBE_SERVER = 'SonarQube'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
    }
}
