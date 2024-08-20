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
        SONAR_HOST_URL = 'http://your-sonarqube-server'
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
                        mvn clean verify sonar:sonar \
                         -Dsonar.projectKey=hellowordapplication \
                         -Dsonar.host.url=http://localhost:9000 \
                         -Dsonar.login=sqp_e416b2afb062e02b47abcac20f29bb6a77092f72
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
