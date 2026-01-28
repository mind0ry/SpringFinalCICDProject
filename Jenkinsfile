pipeline {
	agent any 
	
	environment {
		DOCKER_USER = 'mindory0144'
		IMAGE_NAME= '${DOCKER_USER}/boot-app:latest'
		CONTAINER_NAME= 'boot-app'
	}
	
	stages {
		stage('Checkout'){
			steps{
				echo 'Git Checkout'
				checkout scm
			}
		}
		
		stage('Gradlew Build') {
			steps {
				echo 'Gradlew Build'
				sh '''
					chmod +x gradlew
					./gradlew clean build -x test
				   '''
			}
		}
		
		stage('Docker Build') {
			steps {
				echo 'Docker Image Build'
				sh '''
					docker build -t ${IMAGE_NAME} .
				   '''
			}
		}
		
		stage('Docker Hub Login') {
			steps {
				echo 'DockerHub Login'
				withCredentials([usernamePassword(
					credentialsId: 'dockerhub-credential',
					usernameVariable: 'DOCKER_ID',
					passwordVariable: 'DOCKER_PW'
				)]){
					sh '''
					    echo "DOCKER_ID=$DOCKER_ID,DOCKER_PWD=$DOCKER_PWD"
					    
					    docker login -u $DOCKER_ID --password-stdin
					   '''
				}
			}
		}
		
		stage('DockerHub Push') {
			steps {
				echo 'Docker Hub Push'
				sh '''
					docker push ${IMAGE_NAME}
				   '''
			}
		}
		
		stage('Docker Run') {
			steps {
				echo 'Docker Run'
				sh '''
					docker stop ${CONTAINER_NAME} || true
					docker rm ${CONTAINER_NAME} || true
					
					docker pull ${IMAGE_NAME}
					
					docker run --name ${CONTAINER_NAME} \
					-it -d -p 9090:9090 \
					${IMAGE_NAME}
				   '''
			}
		}
	}
	
	post {
		success {
			echo 'Docker 실행 성공'
		}
		failure {
			echo 'Docker 실행 실패'
		}
	}
}