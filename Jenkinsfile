pipeline {
    agent any

    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

    environment {
        SONARQUBE_ENV = 'sonarqube-server'  // Ensure this matches your SonarQube config ID in Jenkins
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/mrdevops12/test-register-app.git'
            }
        }

        stage('Build Application') {
            steps {
                sh "mvn clean install"
            }
        }

        stage('Run Tests') {
            steps {
                sh "mvn test"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv(env.SONARQUBE_ENV) {
                        sh "mvn sonar:sonar -Dsonar.projectKey=jenkins-maven -Dsonar.java.binaries=server/target/classes,webapp/target/classes"
                    }
                }
            }
        }

        stage('Package Application') {
            steps {
                sh "mvn package"
            }
        }
    }

    post {
        always {
            junit '**/target/surefire-reports/*.xml'
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
        }
    }
}
