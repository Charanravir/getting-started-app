pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = "dockeruser"
        DOCKER_IMAGE = "charanravir/todoapp"
        SSH_CREDENTIALS_ID = "ssh_key"
        SERVER_2_USER = "ubuntu"
        SERVER_2_IP = "172.31.6.89"
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out the repository..."
                git branch: 'main', url: 'https://github.com/charanravir/getting-started-app.git'
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                echo "Building and pushing the Docker image..."
                withDockerRegistry([credentialsId: "${DOCKER_CREDENTIALS_ID}", url: 'https://index.docker.io/v1/']) {
                    sh '''
                    docker build -t ${DOCKER_IMAGE}:v${BUILD_ID} .
                    docker tag ${DOCKER_IMAGE}:v${BUILD_ID} ${DOCKER_IMAGE}:latest
                    docker push ${DOCKER_IMAGE}:v${BUILD_ID}
                    docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }

        stage('Deploy to Server 2') {
            steps {
                echo "Deploying to Server 2..."
                sshagent([SSH_CREDENTIALS_ID]) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ${SERVER_2_USER}@${SERVER_2_IP} "
                        docker pull ${DOCKER_IMAGE}:latest && \
                        docker stop todoapp || true && \
                        docker rm todoapp || true && \
                        docker run -d --name todoapp -p 3000:3000 ${DOCKER_IMAGE}:latest
                    "
                    '''
                }
            }
        }
    }

   
}
