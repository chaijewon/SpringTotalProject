pipeline {
	agent any
	
	environment {
		APP = "spring-app"
		IMAGE = "spring-app"
		PORT = "9090"
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
	    stage('Deploy') {
            steps {
                sh '''
                echo "▶ 기존 컨테이너 종료 (graceful)"
                docker rm -f spring-app || true

                echo "▶ 새 컨테이너 실행"
                docker run -d \
                  --name spring-app \
                  -p 9090:9090 \
                  spring-app:latest
                '''

                sh '''
                echo "▶ Health Check 시작"
                for i in 1 2 3 4 5 6 7 8 9 10
                do
                  if curl -s http://localhost:8080/actuator/health | grep UP; then
                    echo "✅ HEALTH CHECK OK"
                    exit 0
                  fi
                  echo "⏳ 대기중..."
                  sleep 2
                done

                echo "❌ HEALTH CHECK FAIL"
                exit 1
                '''
            }
        }
    }

    post {
        failure {
            echo "❌ 배포 실패"
        }
        always {
            cleanWs()
        }
    }
}