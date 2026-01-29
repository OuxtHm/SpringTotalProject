   pipeline {
      agent any
      
      environment {
         DOCKER_IMAGE = "ouxthm/total-app"
         DOCKER_TAG = "latest"
         EC2_HOST = "13.124.198.168"
         EC2_USER = "ubuntu"
         COMPOSE_FILE = "docker-compose.yml"
      }
      stages{
         stage('Checkout'){
            steps{
               echo 'Git Checkout'
               checkout scm
            }
         }
         
         stage('Gradlew Build') {
            steps{
               echo 'Gradle Build'
               sh """
                   chmod +x gradlew
                   ./gradlew clean build -x test
                  """
            }
         }
         
         stage('Docker build'){
            steps {
               echo 'Docker Image build'
               sh """
                   docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                   
                   """
               
            }
         }
         
         stage('Docker Hub Login'){
            steps {
               echo 'Docker Hub Login'
               withCredentials([usernamePassword(
                  credentialsId: 'dockerhub_config',
                  usernameVariable: 'DOCKER_ID',
                  passwordVariable: 'DOCKER_PW'
               )]){
                  sh """
                      echo $DOCKER_PW | docker login -u $DOCKER_ID --password-stdin
                      docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                     """
               }
               
            }
            
            
         }
         
         stage('Deply docker-compose'){
			steps {
				 sshagent(['SERVER_SSH_KEY']){
				 sh """
					ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} << 'EOF'
					docker rm -f \$(docker ps -q --filter "publish=9090") 2>/dev/null || true
					cd /home/ubuntu/app
					docker-compose down || true
					docker-compose pull
					docker-compose up -d
EOF
				    """
				}
			}
		 }
         
         /*
         stage('Deploy to EC2'){
            steps{
               sshagent(['SERVER_SSH_KEY']){
               sh """
                  ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} << EOF
                       docker stop total-app || true
                       docker rm total-app || true
                       docker pull ${DOCKER_IMAGE}:${DOCKER_TAG}
                       docker run --name total-app -it -d -p 9090:9090 ${DOCKER_IMAGE}:${DOCKER_TAG}
                  """
                
               }
            }
            
         }*/
         
 /*
         stage('Docker Compose Down'){
            steps{
               echo 'Docker-compose down'
               sh """
                  docker-compose -f ${COMPOSE_FILE} down || true
                  """
            }
         }
 

         stage('Docker Stop And RM'){
            steps{
               echo 'docker stop rm'
               sh """
                  docker stop total-app || true
                  docker rm total-app || true
                  docker pull ${DOCKER_IMAGE}
                  """
            }
            
         }  
         stage('Docker Compose up'){
            steps{
               echo 'Docker-compose up'
               sh """
                  docker-compose -f ${COMPOSE_FILE} up -d
                 """
            }
            
         }
         */
         
         /*stage('Docker Run'){
            steps{
               echo 'Docker Run'
               sh """
                  docker stop ${CONTAINER_NAME} || true
                  docker rm ${CONTAINER_NAME} || true
                  
                  docker pull ${IMAGE_NAME}
                  
                  docker run --name ${CONTAINER_NAME} \
                  -it -d -p 9090:9090 \
                  ${IMAGE_NAME}
                  """
               
            }
         }*/
         
          
      
      }
      post {
            success{
               echo 'CI/CD 실행 성공'
            }
            failure {
               echo 'CI/CD 실행 실패'
               
            }
         }
   }