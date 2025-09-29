pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        DOCKERHUB_USER = 'ndiaye2024'
    }

    stages {
        stage('Build Backend Image') {
            steps {
                sh "docker build -t ${DOCKERHUB_USER}/backend:latest ./mon-projet-express"
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh "docker build -t ${DOCKERHUB_USER}/frontend:latest ./"
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'jenkinsauto', variable: 'DOCKERHUB_TOKEN')]) {
                    sh "echo $DOCKERHUB_TOKEN | docker login -u ${DOCKERHUB_USER} --password-stdin"
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                sh "docker push ${DOCKERHUB_USER}/backend:latest"
                sh "docker push ${DOCKERHUB_USER}/frontend:latest"
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh "docker compose -f docker-compose.yml up -d"
            }
        }
    }

    post {
        success {
            echo '✅ Déploiement réussi !'
        }
        failure {
            echo '❌ Échec du pipeline'
        }
    }
}
