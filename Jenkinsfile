pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shoebahmebkhan/attendence-project.git'
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                docker build -t shoebak/attendence-project-backend:latest backend
                docker build -t shoebak/attendence-project-frontend:latest frontend
                '''
            }
        }

        stage('DockerHub Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_PASS'
                )]) {
                    sh '''
                    echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                    docker push shoebak/attendence-project-backend:latest
                    docker push shoebak/attendence-project-frontend:latest
                    '''
                }
            }
        }

        stage('Deploy to Swarm') {
            steps {
                sh '''
                docker pull shoebak/attendence-project-backend:latest
                docker pull shoebak/attendence-project-frontend:latest
                docker stack deploy -c docker-compose.yml attendence
                '''
            }
        }
    }

    post {
        success {
            echo " Deployment successful!"
        }
        failure {
            echo " Deployment failed!"
        }
    }
}



