pipeline {
    agent any

    environment {
        REGISTRY = "docker.io"
        IMAGE_NAME = "ssiraparapu/business-mgmt-app"
        SONAR_HOST_URL = "sai.sonarqube.local"
    }

    stages {

        stage("Build Code") {
            tools {
                maven 'maven-3.9.11'
            }
            steps {
                sh "mvn clean install -DskipTests"
            }
        }
    }
}
