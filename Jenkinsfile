pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        GITHUB_CREDENTIALS = credentials('jenkins-ci')  // si tu utilises un token GitHub
        DOCKERHUB_USER = 'ndiaye2024'
    }

    stages {
        stage('Checkout code') {
            steps {
                // Clonage manuel dans le workspace
                sh 'git clone -b main https://github.com/ndiayekhardiata2024/express_mongo.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                // Dockerfile backend dans ./express_mongo/mon-projet-express
                sh "docker build -t ${DOCKERHUB_USER}/backend:latest ./express_mongo/mon-projet-express"
            }
        }

        stage('Build Frontend Image') {
            steps {
                // Dockerfile frontend à la racine du dépôt
                sh "docker build -t ${DOCKERHUB_USER}/frontend:latest ./express_mongo"
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
                // Le fichier docker-compose.yml doit être dans ./express_mongo
                sh "docker compose -f express_mongo/docker-compose.yml up -d"
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
