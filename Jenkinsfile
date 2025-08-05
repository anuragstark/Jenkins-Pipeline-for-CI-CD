pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/anuragstark/Jenkins-Pipeline-for-CI-CD.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build('earth616-hero')
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    sh 'docker rm -f earth616-container || true'
                    sh 'docker run -d --name earth616-container -p 8080:80 earth616-hero'
                }
            }
        }
    }

    post {
        success {
            echo "Deployed Earth-616 App!"
        }
        failure {
            echo "Build or Deploy failed!"
        }
    }
}
