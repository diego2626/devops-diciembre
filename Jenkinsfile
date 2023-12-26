pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-2'
        EKS_CLUSTER_NAME = 'devops-cluster'
    }
 
    stages {
        stage('SCM') {
            steps {
               git branch: 'master', url:'https://github.com/Stywar/devops-dotnet-account-23'
            }
        }
        
       stage('Builder Image') {
            steps {
                bat "docker build -t diegoi3131/ms-test ."  
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
