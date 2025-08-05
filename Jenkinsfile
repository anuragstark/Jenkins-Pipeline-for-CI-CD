pipeline {
    agent any
    
    environment {
        // Define environment variables
        DOCKER_IMAGE = 'my-web-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = 'web-app-container'
        PORT = '8080'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from repository...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building the application...'
                script {
                    // Build Docker image
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
                }
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                script {
                    // Run container for testing
                    sh "docker run -d --name test-container -p 8081:80 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    
                    // Wait for container to start
                    sleep(time: 10, unit: 'SECONDS')
                    
                    // Test if the application is running
                    sh """
                        curl -f http://localhost:8081 || exit 1
                        echo 'Application is responding successfully!'
                    """
                    
                    // Clean up test container
                    sh "docker stop test-container && docker rm test-container"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                script {
                    // Stop and remove existing container if it exists
                    sh """
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                    """
                    
                    // Deploy new container
                    sh """
                        docker run -d --name ${CONTAINER_NAME} \
                        -p ${PORT}:80 \
                        --restart unless-stopped \
                        ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                    
                    // Verify deployment
                    sleep(time: 5, unit: 'SECONDS')
                    sh "curl -f http://localhost:${PORT} || exit 1"
                    echo "Application deployed successfully at http://localhost:${PORT}"
                }
            }
        }
        
        stage('Cleanup') {
            steps {
                echo 'Cleaning up old Docker images...'
                script {
                    // Keep only the latest 3 builds
                    sh """
                        docker images ${DOCKER_IMAGE} --format 'table {{.Tag}}' | grep -v latest | grep -v TAG | tail -n +4 | xargs -r docker rmi ${DOCKER_IMAGE}: || true
                    """
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed.'
            // Clean up any temporary containers
            sh "docker ps -aq --filter name=test-container | xargs -r docker rm -f || true"
        }
        success {
            echo 'Pipeline succeeded'
            // You can add notifications here (email, Slack, etc.)
        }
        failure {
            echo 'Pipeline failed'
            // Clean up on failure
            sh "docker stop ${CONTAINER_NAME} || true"
            sh "docker rm ${CONTAINER_NAME} || true"
        }
    }
}