pipeline {
    agent any  // Exécute sur l'agent disponible, ici ton container Jenkins

    environment {
        // Nom d'utilisateur Docker Hub
        DOCKERHUB_USER = 'ndiaye2024'
    }

    stages {
        // -----------------------------
        stage('Build Backend Image') {
            steps {
                // Construction de l'image backend
                // ./mon-projet-express doit contenir le Dockerfile backend
                sh """
                    echo '🔹 Construction de l image backend...'
                    docker build -t ${DOCKERHUB_USER}/backend:latest ./mon-projet-express
                """
            }
        }

        // -----------------------------
        stage('Build Frontend Image') {
            steps {
                // Construction de l'image frontend
                // Dockerfile frontend doit être à la racine
                sh """
                    echo '🔹 Construction de l image frontend...'
                    docker build -t ${DOCKERHUB_USER}/frontend:latest ./
                """
            }
        }

        // -----------------------------
        stage('Login to Docker Hub') {
            steps {
                // On récupère le token stocké dans Jenkins Credentials
                withCredentials([string(credentialsId: 'jenkinsauto', variable: 'DOCKERHUB_TOKEN')]) {
                    sh """
                        echo '🔹 Connexion à Docker Hub...'
                        echo $DOCKERHUB_TOKEN | docker login -u ${DOCKERHUB_USER} --password-stdin
                    """
                }
            }
        }

        // -----------------------------
        stage('Push Images to Docker Hub') {
            steps {
                sh """
                    echo '🔹 Push backend...'
                    docker push ${DOCKERHUB_USER}/backend:latest
                    echo '🔹 Push frontend...'
                    docker push ${DOCKERHUB_USER}/frontend:latest
                """
            }
        }

        // -----------------------------
        stage('Deploy with Docker Compose') {
            steps {
                sh """
                    echo '🔹 Déploiement via Docker Compose...'
                    docker compose -f docker-compose.yml up -d
                """
            }
        }
    }

    // -----------------------------
    post {
        success {
            echo '✅ Déploiement terminé avec succès !'
        }
        failure {
            echo '❌ Échec du pipeline ! Vérifie les logs.'
        }
    }
}
