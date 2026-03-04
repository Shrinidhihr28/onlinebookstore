pipeline {
    agent any
    environment {
        DOCKER_HUB_USER = "shrinidhihr28"
        APP_NAME = "onlinebookstore"
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Shrinidhihr28/onlinebookstore.git'
            }
        }
        stage('Maven Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Docker Build & Push') {
            steps {
                sh "docker build -t ${DOCKER_HUB_USER}/${APP_NAME}:latest ."
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push ${DOCKER_HUB_USER}/${APP_NAME}:latest"
                }
            }
        }
        stage('K8s Deployment') {
            steps {
                // Deployment with 3 replicas for your "nodes" requirement
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl rollout restart deployment bookstore-deploy'
            }
        }
    }
}
