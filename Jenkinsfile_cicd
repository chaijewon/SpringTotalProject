pipeline {
    agent any

    environment {
        APP_DIR = "~/app"
        JAR_NAME = "SpringTotalProject-0.0.1-SNAPSHOT.war"
        DOCKER_IMAGE = "chaijewon/total-app:latest"
        K8S_DEPLOYMENT = "totalapp-deployment"
        K8S_YAML = "/var/lib/jenkins/k8s/deployment.yaml"
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
                sh './gradlew clean build'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh 'echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh "docker push ${DOCKER_IMAGE}"
            }
        }
        // minikube 배포
        stage('Deploy to Minikube') {
            steps {
                sh """
                    # Deployment 삭제 (없으면 무시)
                    kubectl delete deployment ${K8S_DEPLOYMENT} || true

                    # 배포 YAML 적용
                    sudo -u sist /usr/local/bin/kubectl apply -f ${K8S_YAML}

                    # 롤아웃 재시작 및 상태 확인
                    sudo -u sist /usr/local/bin/kubectl rollout restart deployment/${K8S_DEPLOYMENT}
                    sudo -u sist /usr/local/bin/kubectl rollout status deployment/${K8S_DEPLOYMENT}
                """
            }
        }
        // 동작 체크 ==> 완료
        stage('Check Minikube Service') {
            steps {
                echo 'Service 완료'
            }
        }
    }
}
