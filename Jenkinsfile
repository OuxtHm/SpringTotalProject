pipeline {
    agent any
    environment {
        SERVER_IP = "3.35.11.88"
        SERVER_USER = "ubuntu"
        APP_DIR = "~/app" // ~ 보다는 절대경로를 추천합니다.
        JAR_NAME = "SpringTotalProject-0.0.1-SNAP.war" 
    }
    
    stages {
        stage('Check Out') {
            steps {
                echo 'Git Checkout'
                checkout scm
            }
        }
        
        stage('Gradle Permission'){
            steps {
                sh 'chmod +x gradlew'
            }
        }
        
        stage('Gradle Build') {
            steps {
                sh './gradlew clean build'
            }
        }
        
        stage('Deploy = rsync') {
            steps {
                sshagent(['SERVER_SSH_KEY']) { // 문법 단순화
                    sh """
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} "mkdir -p ${APP_DIR}"
                        rsync -avz -e 'ssh -o StrictHostKeyChecking=no' build/libs/*.war ${SERVER_USER}@${SERVER_IP}:${APP_DIR}/${JAR_NAME} 
                    """
                }
            }
        }
        
        stage('Run Application') {
            steps {
                sshagent(['SERVER_SSH_KEY']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} << 'EOF'
                            pkill -f '${JAR_NAME}' || true
                            nohup java -jar ${APP_DIR}/${JAR_NAME} > ${APP_DIR}/log.txt 2>&1 &
                            sleep 2
                            exit
EOF
                    """
                }
            }
        }
    }
}