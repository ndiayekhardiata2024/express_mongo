pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'ndiaye2024'
        DOCKER_HUB_PASS = credentials('Hub-Jenkins')
        DOCKER_HUB_REPO = 'ndiaye2024'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[
                        credentialsId: 'github-jenkins',
                        url: 'https://github.com/ndiayekhardiata2024/express_mongo.git'
                    ]]
                )
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t $DOCKER_HUB_REPO/backend:latest ./mon-projet-express'
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh 'docker build -t $DOCKER_HUB_REPO/frontend:latest ./'
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh "echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER"
            }
        }

        stage('Push Images') {
            steps {
                sh 'docker push $DOCKER_HUB_REPO/backend:latest'
                sh 'docker push $DOCKER_HUB_REPO/frontend:latest'
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d'
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }

        success {
            emailext(
                subject: "✅ Build réussi : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le build a réussi.\nVoir les détails ici : ${env.BUILD_URL}",
                to: 'ndiayekhardiata2024@gmail.com'
            )
        }

        failure {
            emailext(
                subject: "❌ Build échoué : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le build a échoué.\nVoir les logs ici : ${env.BUILD_URL}",
                to: 'ndiayekhardiata2024@gmail.com'
            )
        }
    }
}
