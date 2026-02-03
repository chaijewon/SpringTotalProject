pipeline {
	agent any
	
	
	// 전역변수 => ${SERVER_IP}
	environment {
			APP_DIR = "~/app"
			JAR_NAME = "SpringTotalProject-0.0.1-SNAPSHOT.war"
	}
		
	stages {
		
		/*
		     git push  = commit 
		        |
		     web hooks / poll
		        |
		     jenkins (local)
		        |
		      build 
		        |
		      docker build
		      docker push 
		        |
		      minikube 
		        | deployment.yaml update 
		      브라우저 실행  
		     
		*/
		 
		 //연결 확인 = ngrok
		 /*stage('Check Git Info') {
			steps {
				sh '''
				    echo "===Git Info==="
				    git branch
				    git log -1
				   '''
			}
		}*/
		
		stage('Minikube Docker Env'){
			steps {
				  sh '''
                     export MINIKUBE_HOME=/home/jenkins   # Jenkins 홈에 minikube 프로파일 복사 후
                     eval $(minikube docker-env)
                     '''
			}
		}
		
		// 감지 = main : push (commit)
		stage('Check Out') {
			steps {
				 echo 'Git Checkout'
                 checkout scm
			}
		}
		
		// gradle build => war파일을 다시 생성 
		stage('Gradle Permission') {
			steps {
				sh '''
				    chmod +x gradlew
				   '''
			}
		}
		
		// build 시작 
		stage('Gradle Build') {
			steps {
				sh '''
				    ./gradlew build
				   '''
			}
		}
		
		// Docker Build 
		stage('Docker Build') {
			steps {
				    sh '''
				        docker build -t chaijewon/total-app:latest .
				       '''
				}
			}
		
		// 실행 명령 => 명령
		
		stage('Deploy to MiniKube') {
			steps {
				    sh '''
				       kubectl delete deployment chaijewon/total-app || true
				       kubectl apply -f ~/k8s/deployment.yaml
				       '''
				}
			}
		
	}
}