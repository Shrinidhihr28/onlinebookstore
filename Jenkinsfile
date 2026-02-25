pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "shrinidhihr28/onlinebookstore"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$DOCKER_TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$DOCKER_TAG'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no ec2-user@EC2_PUBLIC_IP '
                docker pull $DOCKER_IMAGE:$DOCKER_TAG &&
                docker stop bookstore || true &&
                docker rm bookstore || true &&
                docker run -d -p 8080:8080 --name bookstore $DOCKER_IMAGE:$DOCKER_TAG
                '
                """
            }
        }
    }

    post {
        failure {
            echo "Pipeline Failed"
        }
        success {
            echo "Deployment Successful"
        }
    }
}
