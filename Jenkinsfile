pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'JDK 17'
        scanner 'SonarQube Scanner'
    }
    environment {
        SONARQUBE_SERVER = 'SonarQube'
        SONARQUBE_TOKEN = credentials('sonar_token')
        JAVA_HOME = "${tool 'JDK 17'}"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/pramilasawant/hellowordapplication.git', branch: 'main'
            }
        }
        
        stage('Build') {
            steps {
                dir('hellowordapplication') {
                    sh 'mvn clean install'
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                dir('hellowordapplication') {
                    script {
                        def scannerHome = tool 'SonarQube Scanner'
                        withSonarQubeEnv(SONARQUBE_SERVER) {
                            sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=my_project_key -Dsonar.projectName=My_Project -Dsonar.projectVersion=1.0 -Dsonar.sources=src -Dsonar.host.url=http://your-sonarqube-server -Dsonar.login=${SONARQUBE_TOKEN}"
                        }
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
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
        }
    }
}
