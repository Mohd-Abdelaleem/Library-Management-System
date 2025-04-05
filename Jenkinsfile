pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'mabdelalim/library-management-system' // Your Docker Hub repo
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Mohd-Abdelaleem/Library-Management-System.git' // Your GitHub repo
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'docker-hub-password', variable: 'DOCKER_PASS')]) {
                        sh "echo \$DOCKER_PASS | docker login -u mabdelalim --password-stdin"
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build --no-cache -t $DOCKER_IMAGE db_server'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f k8s/lib-db-secret.yaml
                    kubectl apply -f k8s/lib-db-pv.yaml
                    kubectl apply -f k8s/lib-db-pvc.yaml
                    kubectl apply -f k8s/lib-db-deployment.yaml
                    kubectl apply -f k8s/lib-db-cluster-svc.yaml

                    kubectl apply -f k8s/lib-website-deployment.yaml
                    kubectl apply -f k8s/lib-website-service.yaml
                    kubectl apply -f k8s/lib-website-LB-scv.yaml
                '''
            }
        }
    }
}
