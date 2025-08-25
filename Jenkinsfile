pipeline {
    agent any

    environment {
        REGISTRY = "docker.io"
        IMAGE_NAME = "ssiraparapu/business-mgmt-app"
        // SONAR_HOST_URL = "http://sai.sonarqube.local"
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

                    withSonarQubeEnv('sonar-local') {
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
        stage ("Check Quality Gate") {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage("Upload Artifacts") {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: 'nexus:8081',
                    groupId: 'com.business',
                    version: '0.0.1-SNAPSHOT',   // must match POM
                    repository: 'maven-snapshots',  // snapshot repo
                    credentialsId: 'nexus-creds',
                    artifacts: [
                        [artifactId: 'BusinessProject',    // must match POM
                        classifier: '',
                        file: 'target/BusinessProject-0.0.1-SNAPSHOT.jar',
                        type: 'jar']
                    ]
                )
            }
        }
        
        stage ("Build App Image") {
            steps {
                script {
                
                    // Build Docker image
                    sh "docker build -t ssiraparapu/business-mgmt-app:01 ."
                }
            }
        }
    }
}
