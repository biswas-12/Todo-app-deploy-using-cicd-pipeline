pipeline {
    agent any
    
    stages{
        stage("Code"){
            steps{
                echo "Cloning the Code"
                git url:"https://github.com/biswas-12/Todo-app-cicd.git", branch: "main"
            }
        }
    
        
        stage("Build"){
            steps{
                echo "Build the Image"
                echo "Image BUILDED!!"
                sh "docker build -t todo-app-cicd ."
            }
            
        }
        
        
        stage("Push to DockerHub"){
            steps{
                echo "Pushing to the Docker--Hub"
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                sh "docker tag todo-app-cicd ${env.dockerHubUser}/todo-app-cicd:latest"
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "docker push ${env.dockerHubUser}/todo-app-cicd:latest"
                }
            }
        }
        
        
        stage("Deploy"){
            steps{
                echo "Ready to Deploy"
                sh "docker-compose down && docker-compose up -d"
                echo "Deploy Completed!!"
            }
        }
    }
}
