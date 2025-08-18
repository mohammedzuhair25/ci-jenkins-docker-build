pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'unicorn'   // Change this to your desired image name
        DOCKER_TAG = '${BUILD_NUMBER}'              // You can also use dynamic tags like 'build-${env.BUILD_NUMBER}'
        REGISTRY = 'mzm1'             // Change this to your Docker registry (e.g., 'docker.io', 'registry.example.com')
        REGISTRY_CREDENTIALS = 'docker-credentials' // Jenkins credentials for Docker registry
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from your repository
                git 'https://github.com/mohammedzuhair25/ci-jenkins-docker-build.git'  // Replace with your Git repository URL
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
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh """
                    docker login
                    """
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Log in to Docker registry using Jenkins credentials
                    withDockerRegistry([credentialsId: "${REGISTRY_CREDENTIALS}", url: "https://${REGISTRY}"]) {
                        // Push Docker image to registry
                        sh """
                        docker push ${REGISTRY}/${DOCKER_IMAGE}:${DOCKER_TAG}
                        """
                    }
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

