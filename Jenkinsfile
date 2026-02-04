pipeline {
	agent any
	
	environment {
		APP = "spring-app"
		IMAGE = "spring-app"
		PORT = 9090
	}
	
	stages {
		stage('Git Checkout') {
	            steps {
	                echo "=== Git Checkout ==="
	                checkout scm
	            }
	    }
	
	    stage('Gradle Permission') {
	            steps {
	                sh 'chmod +x gradlew'
	            }
	        }
	
	    stage('Gradle Build') {
	            steps {
	                sh './gradlew build -x test --build-cache'
	            }
	        }
	
	    stage('Docker Build') {
	            steps {
	                sh "docker build -t ${IMAGE}:latest ."
	            }
	        }
	    stage("Deply"){
			steps {
				script {
	                    // 기존 컨테이너 graceful 종료
	                    sh '''
	                    OLD=$(docker ps -q -f name=${APP}
	                    if [ -n "$OLD" ]; then
	                      docker stop $OLD
	                      docker rm $OLD
	                    fi
	                    '''
	
	                    // 새 컨테이너 실행
	                    sh '''
	                    docker run -d --name ${APP} -p ${PORT}:9090 ${IMAGE}:latest
	                    '''
	
	                    // 헬스 체크
	                    sh '''
	                    for i in {1..10}
	                    do
	                      if curl -s http://localhost:9090/actuator/health | grep UP; then
	                        echo "HEALTH CHECK OK"
	                        exit 0
	                      fi
	                      sleep 2
	                    done
	                    exit 1
	                    '''
	                }
			 }
			
		}
		
		 post {
	        failure {
	            echo "배포 실패 - 이전 상태 유지"
	        }
	        always {
	            cleanWs()
	        }
	    }
	  }

}