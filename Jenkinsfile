pipeline{
	agent any
	environment{
		DOCKER_IMAGE="dinhthong0402/yeuhan"
	}
	stages {
		stage('Checkout code') {
            steps {
                checkout scm
            }
        }
		stage('Build and Push'){
			environment {
				DOCKER_TAG="${GIT_BRANCH.tokenize('/').pop()}-${GIT_COMMIT.substring(0,7)}"
			}
			steps{
				sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} . --no-cache'
				sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"	
				sh "docker image ls | grep ${DOCKER_IMAGE}"
				withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
					sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
					sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
					sh "docker push ${DOCKER_IMAGE}:latest"
				}

				//clean to save disk
				sh "docker image rm ${DOCKER_IMAGE}:${DOCKER_TAG}"
				sh "docker image rm ${DOCKER_IMAGE}:latest"
			}
		}
		stage('Deploy'){
			steps{
				sh "/home/ubuntu/workspace/run-yeuhan.sh"
				// sh "sudo kubectl patch deployment ${DEPLOYMENT} -p \"{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"date\":\"$(date
			}
		}
	}
	post {
		success {
		echo "SUCCESSFUL"
		}
		failure {
		echo "FAILED"
		}
	}
}