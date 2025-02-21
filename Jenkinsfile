pipeline {
    agent any
    tools {
        jdk 'Java17'        // Assuming Jenkins has 'Java17' configured
        maven 'Maven3'      // Assuming Jenkins has 'Maven3' configured
    }
    
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()  // Clean previous workspace
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/mrdevops12/test-register-app.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"  // Build the application
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"  // Run tests
            }
        }

        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                        sh '''
                            mvn sonar:sonar \
                            -Dsonar.projectKey=jenkins-maven \
                            -Dsonar.host.url=http://107.21.176.4:9000 \
                            -Dsonar.login=sqa_4d283dbc2899d98e66c57af9a02dbdeac4326637 \
                            -Dsonar.java.binaries=server/target/classes
                        '''
                    }
                }
            }
        }
    }
}
