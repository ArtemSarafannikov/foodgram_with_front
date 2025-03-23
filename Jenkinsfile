pipeline {
    agent any

    environment {
        TELEGRAM_TOKEN = credentials('telegram-bot-access-token')
        TELEGRAM_CHAT_ID = credentials('telegram-chat-id')

        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        PROJECT_NAME = 'foodgram'
        FRONTEND_REPO = 'https://github.com/ArtemSarafannikov/foodgram_with_front.git'
        BACKEND_REPO = 'https://github.com/ArtemSarafannikov/foodgram.git'
        CREDENTIALS_ID = 'jenkins-ssh'
    }

    stages {
        stage('Checkout') {
            steps {
                git(
                    url: "${env.FRONTEND_REPO}",
                    branch: 'master',
                    credentialsId: "${env.CREDENTIALS_ID}"
                )
                dir('backend') {
                    git(
                    url: "${env.BACKEND_REPO}",
                    branch: 'master',
                    credentialsId: "${env.CREDENTIALS_ID}"
                )
                }
            }
        }

        stage('Build Backend') {
            steps {
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} build api'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'docker-compose -f ${DOCKER_COMPOSE_FILE} build frontend'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker-compose -f ${DOCKER_COMPOSE_FILE} stop api frontend
                    docker rm foodgram_api foodgram_frontend
                    docker-compose -f ${DOCKER_COMPOSE_FILE} up -d api frontend
                    docker image prune -f
                '''
            }
        }
    }

    post {
        success {
            script {
                sh '''curl -s -X POST https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage -d chat_id=${TELEGRAM_CHAT_ID} -d text="Build success"'''
            }
        }
        failure {
            script {
                sh '''curl -s -X POST https://api.telegram.org/bot${TELEGRAM_TOKEN}/sendMessage -d chat_id=${TELEGRAM_CHAT_ID} -d text="Build failure"'''
            }
        }
    }
}