pipeline {
    agent any

    environment {
        IMAGE_NAME = "node-${env.BRANCH_NAME}:v1.0"
        PORT = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Installing dependencies..."
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh './scripts/test.sh'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME} ."
                    sh "docker images | grep node-${env.BRANCH_NAME}"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Detener contenedor del mismo entorno si existe
                    sh "docker rm -f ${env.BRANCH_NAME}_app || true"

                    // Correr nuevo contenedor
                    sh "docker run -d -p ${PORT}:${PORT} --name ${env.BRANCH_NAME}_app ${IMAGE_NAME}"
                    echo "App deployed at http://localhost:${PORT}"
                }
            }
        }
    }
}
