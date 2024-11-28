pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "charanravir/todoapp"
        DOCKER_TAG = "21"
        DOCKER_LATEST_TAG = "latest"
        CONTAINER_NAME = "todoapp"
        PORT_MAPPING = "3000:3000"
    }
    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out the repository...'
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building the Docker image...'
                sh """
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                """
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                withDockerRegistry([url: '', credentialsId: 'docker-hub-credentials']) {
                    sh """
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:${DOCKER_LATEST_TAG}
                        docker push ${DOCKER_IMAGE}:${DOCKER_LATEST_TAG}
                    """
                }
            }
        }
        stage('Cleanup Existing Container') {
            steps {
                echo 'Stopping and removing any existing container...'
                sh """
                    if docker ps -q --filter name=${CONTAINER_NAME}; then
                        docker stop ${CONTAINER_NAME}
                        docker rm ${CONTAINER_NAME}
                    else
                        echo "No containers to stop"
                    fi
                """
            }
        }
        stage('Run Docker Container') {
            steps {
                echo 'Running the Docker container...'
                sh """
                    docker run -d --name ${CONTAINER_NAME} -p ${PORT_MAPPING} ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }
    }
    post {
        always {
            echo 'Cleaning up unused Docker images...'
            sh 'docker image prune -f'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
