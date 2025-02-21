pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/mrdevops12/test-register-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-scanner') {
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
