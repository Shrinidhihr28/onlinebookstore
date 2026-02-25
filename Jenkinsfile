pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "shrinidhihr28/onlinebookstore"
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
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
    credentialsId: 'dockerhub-password',
    usernameVariable: 'DOCKER_USER',
    passwordVariable: 'DOCKER_PASS'
)]) {
    sh '''
        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
        docker push shrinidhihr28/onlinebookstore
    '''
}
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                docker stop onlinebookstore || true
                docker rm onlinebookstore || true
                docker run -d -p 80:8080 --name onlinebookstore $DOCKER_IMAGE
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline Successful'
        }
        failure {
            echo 'Pipeline Failed'
        }
    }
}
