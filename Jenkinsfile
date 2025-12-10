pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "jenkins-demo-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = "jenkins-demo-container"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo "Récupération du code source..."
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                echo "Installation des dépendances..."
                sh 'npm install'
            }
        }
        
        stage('Run Tests') {
            steps {
                echo "Ecécution des tests..."
                sh 'npm run test'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "Construction de l'image Docker..."
                sh 'docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .'
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Déploiement du conteneur..."
                sh """
                    if [ \$(docker ps -q -f name=${CONTAINER_NAME}) ]; then
                        echo "Arrêt de l'ancien conteneur..."
                        docker stop ${CONTAINER_NAME}
                    fi
                """
    
                sh """
                    if [ \$(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                        echo "Suppression de l'ancien conteneur..."
                        docker rm ${CONTAINER_NAME}
                    fi
                """
    
                sh """
                    echo "Lancement du nouveau conteneur..."
                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p 3000:3000 \
                        ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }
    }
    
    post {
        success { echo "Pipeline terminée avec succès !" }
        failure { echo "La pipeline a échoué." }
    }
}
