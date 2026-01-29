pipeline {
	agent any
	environment {
			SERVER_IP = "3.35.11.88"
			SERVER_USER = "ubuntu"
			APP_DIR = "~/app"
			JAR_NAME = "SpringTotalProject-0.0.1-SNAP.war"
		}
		
	stages {
		stage('Check Out') {
			stpes {
				git branch: 'main',
				url: 'https://github.com/OuxtHm/SpringTotalProject.git'
			}
		}
		
		stage('Gradle Permission'){
			steps {
				sh '''
					chmod +x gradlew
				   '''
			}
		}
		
		stage('Gradle Build') {
			steps {
				hs '''
					./gradlew clean build
				   '''
			}
		}
		
		stage('Deploy = rsync') {
			steps {
				sshagent(credentials:['SERVER_SSH_KEY']){
					sh """
						rsync -avz -e "ssh -o StrictHostKeyChecking=no" \
                  		build/libs/*.jar ${SERVER_USER}@${SERVER_IP}:${APP_DIR} 
					   """
				}
			}
		}
		
		stage('Run Application') {
			steps {
				sshagent(credentials:['SERVER_SSH_KEY']){
					sh """
						ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} << 'EOF'
						   pkill -f 'java -jar' || true
						   nohup java -jar ${APP_DIR}/${JAR_NAME} > log.txt 2>&l &
						EOF
						
					   """
				}
			}
		}
	}
} 