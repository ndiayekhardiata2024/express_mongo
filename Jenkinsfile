pipeline {
    agent any   // L'agent = machine/VM/container où le pipeline tourne (ici ton conteneur Jenkins)

    environment {
        // On récupère les identifiants stockés dans Jenkins (Manage Jenkins > Credentials)
    // login/pwd Docker Hub
        GITHUB_CREDENTIALS = credentials('jenkins-ci')         // token GitHub si besoin
        DOCKERHUB_USER = 'ndiaye2024'               // Change par ton username Docker Hub
    }

    stages {
        stage('Checkout code') {
            steps {
                // On clone le dépôt GitHub. 
                // NOTE : Le webhook est côté GitHub, pas dans ce fichier.
                // Quand tu push sur GitHub, GitHub déclenche Jenkins via l’URL webhook.
                git branch: 'main',
                    
                    url: 'https://github.com/ndiayekhardiata2024/express_mongo.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                script {
                    // On construit l’image backend à partir du Dockerfile dans ./backend
                    // Exemple : docker build -t saratou/backend:latest ./mon-projet-express
                    sh "docker build -t ${DOCKERHUB_USER}/backend:latest ./mon-projet-express"
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                script {
                    // Idem pour le frontend
                    sh "docker build -t ${DOCKERHUB_USER}/frontend:latest ./"
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'jenkinsauto', variable: 'DOCKERHUB_TOKEN')]) {
                    // On se connecte à Docker Hub avec les credentials Jenkins
                    sh "echo $DOCKERHUB_TOKEN | docker login -u ${DOCKERHUB_USER} --password-stdin"
                }

            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                script {
                    // On pousse les images sur Docker Hub
                    sh "docker push ${DOCKERHUB_USER}/backend:latest"
                    sh "docker push ${DOCKERHUB_USER}/frontend:latest"
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    // Ici, on déploie avec docker-compose
                    // ⚠️ Différence importante :
                    // - Les étapes "docker build" servent à CRÉER et PUSHER les images vers Docker Hub
                    // - Ici, docker-compose se contente de lancer les conteneurs (il peut aussi rebuild si on met --build)
                    // Si tu veux que le déploiement tire directement du Docker Hub :
                    // Ton docker-compose.yml doit référencer les images : saratou/backend:latest et saratou/frontend:latest
                    sh "docker compose -f docker-compose.yml up -d"
                }
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
