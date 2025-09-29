pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'ndiaye2024'
    }

    stages {
        stage('Build Backend Image') {
            steps {
                // Dockerfile backend dans ./mon-projet-express
                sh "docker build -t ${DOCKERHUB_USER}/backend:latest ./mon-projet-express"
            }
        }

        stage('Build Frontend Image') {
            steps {
                // Dockerfile frontend à la racine
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
