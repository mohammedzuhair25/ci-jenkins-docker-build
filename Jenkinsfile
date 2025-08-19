pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'unicorn'   // Change this to your desired image name
        DOCKER_TAG = '${BUILD_NUMBER}'              // You can also use dynamic tags like 'build-${env.BUILD_NUMBER}'
        NEXUS_URL = 'mzm-xps-13-9380:7000'
        NEXUS_REPO = 'docker-private'  // Change this to your actual Nexus repository name
        DOCKER_REGISTRY = 'localhost:7000'  // Nexus Docker registry
        DOCKER_CREDENTIALS = 'nexus-docker-private' // Jenkins credentials for Docker registry



    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from your repository
                git branch: 'main', url:'https://github.com/mohammedzuhair25/ci-jenkins-docker-build.git'  // Replace with your Git repository URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh """
                    docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG} .
                    """
                }
            }
        }
        stage('Login to Nexus Docker Registry') {
            steps {
                script {
                withCredentials([usernamePassword(credentialsId: 'nexus-docker-private', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                sh """
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin http://localhost:7000
                """
                    }
                }
            }
        }

        stage('Push Image to Nexus') {
            steps {
                script {
                    // Tag the Docker image with Nexus repository details
                    def image = docker.image("${IMAGE_NAME}:${IMAGE_TAG}")
                    image.tag("${DOCKER_REGISTRY}/${NEXUS_REPO}/${IMAGE_NAME}:${IMAGE_TAG}")
                    
                    // Push the Docker image to Nexus
                    image.push("${IMAGE_TAG}")
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker images after the build
            sh 'docker system prune -f'
        }
        success {
            echo 'Docker image built and pushed successfully!'
        }
        failure {
            echo 'Build or push failed.'
        }
    }
}

