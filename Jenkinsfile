pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQubeServer'  // The name of the SonarQube server configured in Jenkins
        SONAR_CREDENTIALS_ID = 'sonarqube_token' // Store the token securely
        DOCKERHUB_CREDENTIALS_ID = '904663fb-32f1-455a-a8d3-20e1f645c356'
        DOCKERHUB_REPO = 'jonnerop/sonarqube'
        SONAR_PROJECT_KEY = 'devops-demo'
        SONAR_PROJECT_NAME = 'DevOps-Demo'
        SONAR_HOST_URL = 'http://localhost:9000/'
        DOCKER_IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Jonnerop/sep2_week5_inclass_s2.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
                            steps {
                                withCredentials([string(credentialsId: "${SONAR_CREDENTIALS_ID}", variable: 'SONAR_TOKEN')]) {
                                    withSonarQubeEnv("${SONARQUBE_SERVER}") {
                                        bat """
                                            sonar-scanner ^
                                            -Dsonar.projectKey=${SONAR_PROJECT_KEY} ^
                                            -Dsonar.sources=src ^
                                            -Dsonar.projectName=${SONAR_PROJECT_NAME} ^
                                            -Dsonar.host.url=${SONAR_HOST_URL} ^
                                            -Dsonar.login=${SONAR_TOKEN} ^
                                            -Dsonar.java.binaries=target/classes
                                        """
                                    }
                                }
                            }
                        }

        stage('Build Docker Image') {
                    steps {
                        script {
                            docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}")
                            // Or specify Dockerfile path explicitly if needed
                            // docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}", "-f ./Dockerfile .")
                        }
                    }
                }

                stage('Push Docker Image to Docker Hub') {
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS_ID) {
                                docker.image("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}").push()
                            }
                        }
                    }
                }
    }
}
