pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'JDK 17'
    }
    environment {
        SONARQUBE_SERVER = 'SonarQube'
        JAVA_HOME = "${tool 'JDK 17'}"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
        SONARQUBR_TOKEN= 'sonar_token'
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
                        withCredentials([string(credentialsId: 'sonar_token', variable: 'SONARQUBE_TOKEN')]) {
                            def token = SONARQUBE_TOKEN // Assign token to a variable
                            withSonarQubeEnv(SONARQUBE_SERVER) {
                                sh "${scannerHome}/bin/sonar-scanner -Dsonar.login=${token}"
                            }
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
