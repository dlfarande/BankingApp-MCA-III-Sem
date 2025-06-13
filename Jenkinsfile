pipeline {
    agent { label 'Slave_Node1' }

    tools {
        maven 'maven-3.6.3'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub_credentials')
        IMAGE_NAME = 'dlfarande/dlf-bankapp-mca-app'
        GIT_REPO = 'https://github.com/dlfarande/BankingApp-MCA-III-Sem.git'
    }

    stages {
        stage('SCM Checkout') {
            steps {
                echo 'Perform SCM Checkout'
                git "${GIT_REPO}"
            }
        }

        stage('Maven Build') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }

        stage("Docker Build & Tag") {
            steps {
                sh 'docker version'
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
                sh 'docker images'
                sh "docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest"
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
    }
}
