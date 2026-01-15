pipeline {
    agent any

    environment {
        DOCKERHUB_USER = credentials('dockerhub-username')
        DOCKERHUB_PASS = credentials('dockerhub-password')
        PROJECT_NAME   = "attendence-project"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/shoebahmebkhan/attendence-project.git'
            }
        }

        stage('Build Images') {
            steps {
                sh "docker build -t ${DOCKERHUB_USER}/${PROJECT_NAME}-backend:latest -f Dockerfile.backend ."
                sh "docker build -t ${DOCKERHUB_USER}/${PROJECT_NAME}-frontend:latest -f Dockerfile.frontend ."
            }
        }

        stage('DockerHub Login & Push') {
            steps {
                sh "echo ${DOCKERHUB_PASS} | docker login -u ${DOCKERHUB_USER} --password-stdin"
                sh "docker push ${DOCKERHUB_USER}/${PROJECT_NAME}-backend:latest"
                sh "docker push ${DOCKERHUB_USER}/${PROJECT_NAME}-frontend:latest"
            }
        }

        stage('Deploy to Swarm') {
            steps {
                sh "export DOCKERHUB_USER=${DOCKERHUB_USER}"
                sh "docker pull ${DOCKERHUB_USER}/${PROJECT_NAME}-backend:latest"
                sh "docker pull ${DOCKERHUB_USER}/${PROJECT_NAME}-frontend:latest"
                sh "docker stack deploy -c docker-compose.yml attendence"
            }
        }
    }

    post {
        success { echo "Deployment successful!" }
        failure { echo "Deployment failed!" }
    }
}



