pipeline {
    agent any
    tools {
        maven 'maven'  // Ensure this matches the Maven name in Jenkins Global Tool Configuration
        jdk 'JDK 17'  // Ensure this matches the JDK name in Jenkins Global Tool Configuration
    }
    environment {
        SONARQUBE_SERVER = 'SonarQube'  // Ensure this matches the name given during SonarQube server configuration in Jenkins
        JAVA_HOME = "${tool 'JDK 17'}"  // Set JAVA_HOME to the correct JDK path
        PATH = "${JAVA_HOME}/bin:${env.PATH}"  // Add JAVA_HOME to the PATH
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
                        withCredentials([string(credentialsId: 'squ_aadae9a5c495b87449d4d4a5356b62b6ac00734a', variable: 'sonar_id')]) {
                            withSonarQubeEnv(SONARQUBE_SERVER) {
                                sh """${scannerHome}/bin/sonar-scanner \
                                    -Dsonar.projectKey=your_project_key \
                                    -Dsonar.sources=. \
                                    -Dsonar.host.url=${env.SONAR_HOST_URL} \
                                    -Dsonar.login=${sonar_id}"""
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
