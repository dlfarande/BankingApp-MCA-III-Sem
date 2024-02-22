pipeline {
    agent { label 'Slave_Node1' }
    
   tools {
        // Define Maven tool installation
        maven 'maven-3.6.3'
    }
    environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerhub_credentials')
	} 

    stages {
        stage('SCM Checkout') {
            steps {
		echo 'Perform SCM Checkout'
           	git 'https://github.com/dlfarande/BankingApp.git'
            }
        }
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
       stage("Docker build"){
            steps {
				sh 'docker version'
				sh "docker build -t dlfarande/dlf-bankapp-eta-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag dlfarande/dlf-bankapp-eta-app:${BUILD_NUMBER} dlfarande/dlf-bankapp-eta-app:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Push2DockerHub') {

			steps {
				sh "docker push dlfarande/dlf-bankapp-eta-app:latest"
			}
		}
        stage('Deploy to Kubernetes Dev Environment') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'Kubernetes', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetesdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		       }
            }
    	}
    }
}
