pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'unicorn'   // Change this to your desired image name
        DOCKER_TAG = '${BUILD_NUMBER}'              // You can also use dynamic tags like 'build-${env.BUILD_NUMBER}'
        NEXUS_REPO = 'localhost:7001'             // Change this to your Docker registry (e.g., 'docker.io', 'registry.example.com')
        REGISTRY_CREDENTIALS = 'nexus-docker-private' // Jenkins credentials for Docker registry

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
                    docker build -t ${REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG} .
                    """
                }
            }
        }
        stage('Login to Nexus Docker Registry') {
            steps {
                script {
                    // Login to Nexus Docker registry using Jenkins credentials
                    docker.withRegistry("https://${NEXUS_REPO}", "${DOCKER_CREDENTIALS}") {
                        // Push will be done in the next step
                    }
                }
            }
        }

        stage('Push Image to Nexus') {
            steps {
                script {
                    // Tag the Docker image with Nexus repository details
                    def image = docker.image("${IMAGE_NAME}:${IMAGE_TAG}")
                    image.tag("${NEXUS_REPO}/${IMAGE_NAME}:${IMAGE_TAG}")
                    
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

