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
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                bat 'bash scripts/test.sh' // ← si tienes Git Bash o WSL
                // o reemplaza por comandos nativos de Windows si no tienes bash
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t ${env.IMAGE_NAME} ."
                    bat "docker images"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    bat "docker rm -f ${env.BRANCH_NAME}_app || echo No container to remove"
                    bat "docker run -d -p ${env.PORT}:${env.PORT} --name ${env.BRANCH_NAME}_app ${env.IMAGE_NAME}"
                    echo "App deployed at http://localhost:${env.PORT}"
                }
            }
        }
    }
}
