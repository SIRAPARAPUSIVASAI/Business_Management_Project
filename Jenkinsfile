pipeline {
    agent any

    environment {
        REGISTRY = "docker.io"
        IMAGE_NAME = "ssiraparapu/business-mgmt-app"
        SONAR_HOST_URL = "http://sai.sonarqube.local:9000"
    }

    stages {

        stage("Build Code") {
            steps {
                sh "mvn clean install -DskipTests"
            }
        }
        stage("Run Code Scanning") {
            steps {
                script {
                    // resolve the Sonar Scanner installation path
                     def scannerHome = tool name: 'sonar-scanner-7.2.0', type: 'hudson.plugins.sonar.SonarRunnerInstallation'

                    withSonarQubeEnv('sonar-jenkins-creds') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=business-mgmt-app \
                            -Dsonar.projectName=business-mgmt-app \
                            -Dsonar.sources=src \
                            -Dsonar.java.binaries=target/classes
                        """
                    }
                }
            }
        }
    }
}
