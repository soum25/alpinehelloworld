pipeline  {
	environment {
		IMAGE_NAME = "alpinehelloworld"
		IMAGE_TAG = "latest"
		IMAGE_REPO = "soum25"
	}
	agent none
	stages {
		stage('Build image') {
			agent any
			steps {
				script {
					sh 'docker build -t $IMAGE_REPO/$IMAGE_NAME:$IMAGE_TAG .'
				}
			}
		}
		stage('Run container based on builded image') {
			agent any
			steps {
				script {
					sh '''
					docker run --name $IMAGE_NAME -d -p 80:5000 -e PORT=5000 $IMAGE_REPO/$IMAGE_NAME:$IMAGE_TAG
					sleep 5
					'''
				}
			}
		}
		stage('Test container') {
			agent any
			steps {
				script {
					sh '''
					var=$(curl http://172.17.0.1:5000)
					if [ "$var" = 'Hello world!' ]; then exit 0; else exit 1; fi
					'''
				}
			}
		}
		stage('Clean Container') {
			agent any
			steps {
				script {
					sh 'docker rm -f ${IMAGE_NAME}'
				}
			}
		}
		stage('deploy with ansible') {
			agent { docker { image 'dirane/docker-ansible:latest' } }
			steps {
				script {
					sh '''
					cd ansible
					ansible-playbook -i prod.yml alpinehelloworld.yml
					'''
				}
			}
		}
		stage('test application') {
			agent { docker { image 'dirane/docker-ansible:latest' } }
			steps {
				script {
					sh '''
					cd ansible
					ansible-playbook -i prod.yml test.yml
					'''
				}
			}
		}	
	}
}
