pipeline{
    agent any
    
    stages{
        stage("Code checkout"){
        steps{
            git branch: 'main', url: 'https://github.com/Charanravir/getting-started-app.git'
        }
            
        }
        
        stage("image build"){
            steps{
                sh 'docker image build -t charanravir/todoapp:v$BUILD_ID .'
                sh 'docker image tag charanravir/todoapp:v$BUILD_ID charanravir/todoapp:latest'
            }
        }
        
        
        
        
        stage("container creation"){
            steps{
                sh 'docker run -itd --name todoapp -p 3000:3000 charanravir/todoapp:latest'
            }
        }
        
        }
    }
    
    
