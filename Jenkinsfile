pipeline {
    agent any
    tools {
        jdk 'Java17'        // Ensure this JDK is configured in Jenkins
        maven 'Maven3'      // Ensure this Maven version is configured in Jenkins
    }
    
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()  // Clean workspace before build
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/mrdevops12/test-register-app.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean install -DskipTests"  // Build all modules without tests initially
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"  // Run tests separately
            }
        }

        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                        sh '''
                            mvn org.sonarsource.scanner.maven:sonar-maven-plugin:4.0.0.4121:sonar \
                            -Dsonar.projectKey=jenkins-maven \
                            -Dsonar.host.url=http://107.21.176.4:9000 \
                            -Dsonar.login=sqa_4d283dbc2899d98e66c57af9a02dbdeac4326637 \
                            -Dsonar.java.binaries=server/target/classes,webapp/target/classes
                        '''
                    }
                }
            }
        }
    }
}
