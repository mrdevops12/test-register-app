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

        stage('Package Application') {
            steps {
                sh "mvn package"
            }
        }
    }

    
}
