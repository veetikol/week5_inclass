pipeline {
    agent any
    environment {
        SONARQUBE_SERVER = 'SonarQube Server'
        SONAR_TOKEN = 'sonar_key'
        IMAGE_NAME='veetikol/devopsdemo'
        IMAGE_TAG='latest'
        DOCKERHUB_CREDENTIALS='docker-cred-v2'
        DOCKERHUB_REPO='veetikol/devopsdemo'
        DOCKER_IMAGE_TAG='latest'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'Main', url: 'https://github.com/veetikol/week5_inclass.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
                sh 'mvn clean package'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube Server') {
                    sh 'sonar-scanner -D sonar.projectKey=devops-demo -D sonar.sources=. -D sonar.host.url=http://127.0.0.1:9000 -D sonar.token=squ_5e93aa7e750df7d24c98cf5b966206fc8f64b51b'
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    sh 'docker build -t veetikol/devopsdemo:latest .'
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                        sh 'echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin'
                        sh 'docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}'
                        sh 'docker push ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}'
                    }
                }
            }
        }
    }
}
