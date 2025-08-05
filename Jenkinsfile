pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'my-web-app'
        DOCKER_TAG = "${BUILD_NUMBER}"
        CONTAINER_NAME = 'web-app-container'
        PORT = '8090'
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
                echo 'Building the Docker image...'
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
                    echo "Docker image built successfully: ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing the application...'
                script {
                    // Run a quick test container
                    sh "docker run --rm -d --name test-container -p 8081:80 ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    
                    // Wait for container to start
                    sleep(time: 10, unit: 'SECONDS')
                    
                    // Simple test - check if container is running
                    sh "docker ps | grep test-container"
                    echo 'Container is running successfully!'
                    
                    // Clean up test container
                    sh "docker stop test-container || true"
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                script {
                    // Stop existing container if running
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                    
                    // Deploy new container
                    sh """
                        docker run -d --name ${CONTAINER_NAME} \
                        -p ${PORT}:80 \
                        --restart unless-stopped \
                        ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                    
                    echo "Application deployed successfully!"
                    echo "Access your application at: http://localhost:${PORT}"
                }
            }
        }
        
        stage('Verify Deployment') {
            steps {
                echo 'Verifying deployment...'
                script {
                    sleep(time: 5, unit: 'SECONDS')
                    sh "docker ps | grep ${CONTAINER_NAME}"
                    echo "Deployment verified - container is running!"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline succeeded'
            echo "Your application is running at: http://localhost:${PORT}"
        }
        failure {
            echo 'Pipeline failed'
            echo 'Cleaning up...'
            script {
                sh "docker stop ${CONTAINER_NAME} || true"
                sh "docker rm ${CONTAINER_NAME} || true"
            }
        }
    }
}