pipeline {
	agent any
	environment { // GIVE THESE VALUES
		containerName="flask-app-alpine";
		imageName="eu.gcr.io/lbg-cloud-incubation/flask-app-alpine";
	}
	stages{
		stage('Docker Build'){
			steps{
			sh '''
			docker build -t $containerName:latest -t $containerName:build-$BUILD_NUMBER .
			'''
			}
		}
		// stage('Push Images'){
		// 	steps{
		// 	sh '''
		// 	docker push $imageName:latest
		// 	docker push $imageName:build-$BUILD_NUMBER
		// 	'''
		// 	}
        //         }
        stage('Stopping container'){
			steps{
			    script {
					if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.142.90.72 << EOF
						docker rm -f $containerName
						'''
					} else if ("${GIT_BRANCH}" == 'origin/development') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.105.192.100 << EOF
						docker rm -f $containerName
						'''
					}
					}
                }
		}
		stage('Restart app'){
			steps{
			script {
					if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.142.90.72 << EOF
						docker run -d -p 80:5500 --name $containerName $containerName:latest
						'''
					} else if ("${GIT_BRANCH}" == 'origin/development') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.105.192.100 << EOF
						docker run -d -p 80:5500 --name $containerName $containerName:latest
						'''
					}
				}
			}
		}
	}
}