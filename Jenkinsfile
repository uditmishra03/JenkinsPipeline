pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'  // Jenkins credentials ID for Docker Hub
        DOCKER_IMAGE_NAME = 'uditmishra03/app-build'         // Replace with your Docker Hub username/repository
        DOCKER_TAG = 'latest'                                // You can also use version numbers or commit SHA here
    }

    stages {
            stage('Install Docker CLI') {

                steps {

                    script {

                        // Install Docker CLI if not already installed (example for Linux)

                        sh '''

                        if ! command -v docker --version &> /dev/null

                        then

                        sudo apt update

                        curl -fsSL https get docker com -o get-docker.sh

                        sudo sh get-docker.sh

                        else

                        echo "Docker is already installed."

                        fi

                        sudo dockerd --ip6tables=false &

                        '''

                    }

                }

        }
        stage('Checkout') {
            steps {
                checkout scm  // This checks out the code from your repository
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh "docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub using Jenkins credentials
                    withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the image to Docker Hub
                    sh "docker push ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    // Optionally, clean up by removing the local image
                    sh "docker rmi ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}"
                }
            }
        }
    }

    post {
        always {
            // Clean up any running Docker containers
            sh 'docker system prune -af'
        }
    }
}

