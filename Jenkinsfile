pipeline {
    agent any
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/mrdevops12/test-register-app.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }

        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv('sonarqube-server') {  // Correct SonarQube server reference
                        sh """
                        mvn sonar:sonar \
                        -Dsonar.projectKey=sqp_7f29f996fc8521fc686c58c7be78a94023415aa6 \
                        -Dsonar.projectName=tt-maven \
                        -Dsonar.host.url=http://107.21.176.4:9000
                        """
                    }
                }
            }
        }
    }
}
