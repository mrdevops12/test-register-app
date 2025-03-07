pipeline {
    agent any

    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

    environment {
        SONARQUBE_ENV = 'sonarqube-server'
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "rajuvarma"
        DOCKER_CREDENTIALS = 'dockerhub'  // Must exist in Jenkins
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
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

        stage('Package Application') {
            steps {
                sh "mvn package"
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS) {
                        def docker_image = docker.build("${IMAGE_NAME}")
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }

        stage("Trivy Scan") {
            steps {
                script {
                    sh "docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ${IMAGE_NAME}:latest --no-progress --scanners vuln --exit-code 0 --severity HIGH,CRITICAL --format table"
                }
            }
        }

        stage('Cleanup Artifacts') {
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker rmi ${IMAGE_NAME}:latest"
            }
        }

        stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh """
                       curl -v -k --user Mahesh:${JENKINS_API_TOKEN} \
                       -X POST \
                       -H 'cache-control: no-cache' \
                       -H 'content-type: application/x-www-form-urlencoded' \
                       --data 'IMAGE_TAG=${IMAGE_TAG}' \
                       'http://ec2-54-167-245-164.compute-1.amazonaws.com:8080/job/test-cd/buildWithParameters?token=gitops-token'
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build and Docker push succeeded!"
        }
        failure {
            echo "❌ Build failed. Check the logs above."
        }
    }
}
