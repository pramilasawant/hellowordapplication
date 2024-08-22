pipeline {
    agent any
    tools {
        maven 'maven'
        jdk 'JDK 17'
    }
    environment {
        SONARQUBE_SERVER = 'SonarQube'
        JAVA_HOME = "${tool 'JDK 11'}"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
        SONAR_HOST_URL = 'http://192.168.49.1:9000'
        SONAR_LOGIN = 'sqp_e416b2afb062e02b47abcac20f29bb6a77092f72' // SonarQube token  // Ensure this token is correct and has permissions
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
                withSonarQubeEnv('SonarQube') { // 'SonarQube' is the name of the SonarQube server configured in Jenkins
                    dir('hellowordapplication') {
                        sh """
                        mvn clean verify sonar:sonar \
                            -Dsonar.projectKey=hellowordapplication \
                            -Dsonar.host.url=${SONAR_HOST_URL} \
                            -Dsonar.login=${SONAR_LOGIN} \
                            -X
                        """
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
