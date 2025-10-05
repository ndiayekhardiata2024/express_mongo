pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'ndiaye2024'
        DOCKER_HUB_PASS = credentials('Hub-Jenkins') // à créer dans Jenkins
        DOCKER_HUB_REPO = 'ndiaye2024'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-jenkins', url: 'https://github.com/ndiayekhardiata2024/express_mongo.git']])
            }
        }

        stage('Build Backend Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_HUB_REPO/backend:latest ./mon-projet-express'
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_HUB_REPO/frontend:latest ./'
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
                    sh "echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin"
                }
            }
        }

        stage('Push Images') {
            steps {
                script {
                    sh 'docker push $DOCKER_HUB_REPO/backend:latest'
                    sh 'docker push $DOCKER_HUB_REPO/frontend:latest'
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh 'docker compose down || true'
                    sh 'docker compose up -d'
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }

     post {
        success {
            emailext (
                subject: "✅ Build réussi : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                <h2>Build réussi 🎉</h2>
                <p>Le job <b>${env.JOB_NAME}</b> (build #${env.BUILD_NUMBER}) s’est terminé avec succès.</p>
                <p>🔗 <a href="${env.BUILD_URL}">Voir les détails dans Jenkins</a></p>
                """,
                to: "ndiayekhardiata2024@gmail.com"
            )
        }

        failure {
            emailext (
                subject: "❌ Build échoué : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                <h2>Échec du build 🚨</h2>
                <p>Le job <b>${env.JOB_NAME}</b> a échoué.</p>
                <p>Vérifie les logs ici :</p>
                <p>🔗 <a href="${env.BUILD_URL}">Consulter le build</a></p>
                """,
                to: "ndiayekhardiata2024@gmail.com"
            )
        }

        unstable {
            emailext (
                subject: "⚠️ Build instable : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                <h2>Build instable ⚠️</h2>
                <p>Le job <b>${env.JOB_NAME}</b> est terminé, mais certains tests ont échoué.</p>
                <p>🔗 <a href="${env.BUILD_URL}">Voir les détails</a></p>
                """,
                to: "ndiayekhardiata2024@gmail.com"
            )
        }
    }   
}
