pipeline {
    agent any

    environment {
        APP_DIR = "/var/lib/jenkins/app"          // Jenkins 작업 디렉토리
        JAR_NAME = "SpringTotalProject-0.0.1-SNAPSHOT.war"
        DEPLOYMENT_NAME = "total-app"            // Deployment 이름
        KUBECONFIG = "/var/lib/jenkins/.kube/config" // Jenkins kubeconfig
        CONTAINER_PORT = "9090"                  // Pod containerPort
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Git Checkout'
                checkout scm
            }
        }

        stage('Gradle Permission') {
            steps {
                sh 'chmod +x gradlew'
            }
        }

        stage('Build WAR') {
            steps {
                sh './gradlew build'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    # Minikube Docker 환경 연결
                    eval $(minikube docker-env)
                    
                    # Docker build
                    docker build -t ${DEPLOYMENT_NAME}:latest .
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                    export KUBECONFIG=${KUBECONFIG}

                    # 기존 Deployment 삭제 (Blue/Green 적용시 true)
                    kubectl delete deployment ${DEPLOYMENT_NAME} || true

                    # Deployment 적용
                    kubectl apply -f /var/lib/jenkins/k8s/deployment.yaml
                '''
            }
        }

    }
}
