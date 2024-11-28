pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "charanravir/todoapp"  // Docker image name
        DOCKER_CREDENTIALS_ID = "dockeruser" // Credentials ID for Docker Hub
    }

    stages {
        stage('Git Checkout') {
            steps {
                echo "Checking out the repository..."
                git branch: 'main', url: 'https://github.com/Charanravir/getting-started-app.git'
            }
        }

        stage('Image Build') {
            steps {
                echo "Building the Docker image..."
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_ID}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                withDockerRegistry([credentialsId: "${DOCKER_CREDENTIALS_ID}", url: 'https://index.docker.io/v1/']) {
                    script {
                        dockerImage.push("${BUILD_ID}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Cleanup Existing Container') {
    steps {
        echo 'Stopping and removing any existing container...'
        script {
            sh '''
            docker ps -q --filter "name=your-container-name" | grep -q . && docker stop your-container-name && docker rm your-container-name || echo "No containers to stop"
            '''
        }
    }
}


        stage('Run Container') {
            steps {
                echo "Running the Docker container..."
                script {
                    docker.run("-d --name todoapp -p 3000:3000 ${DOCKER_IMAGE}:${BUILD_ID}")
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully. The container is running, and the image has been pushed to Docker Hub."
        }
        failure {
            echo "Pipeline failed. Check the logs for details."
        }
        always {
            echo "Cleaning up unused Docker images..."
            sh "docker image prune -f" // Removes unused Docker images to free up space
        }
    }
}
