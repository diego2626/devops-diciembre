pipeline {
    agent any

    stages {
        stage('SCM') {
            steps {
               git branch: 'master', url:'https://github.com/Stywar/devops-dotnet-account-23'
            }
        }
        
       stage('Builder Image') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'dockercrentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    bat "docker login --username %DOCKER_USERNAME% --password %DOCKER_PASSWORD%"
                    bat "docker build -t diegoi3131/ms-test ."
                }  
            }
        }
        
        stage('Deploy EKS') {
            steps {
                    bat 'kubectl config use-context arn:aws:eks:us-east-2:964904475269:cluster/devops-cluster'
                    bat 'aws eks --region us-east-2 update-kubeconfig --name devops-cluster' 
                    bat 'kubectl apply -f k8s.yml'
                    bat 'kubectl rollout restart deployment app-deployment'
            }
        }
    }
}
