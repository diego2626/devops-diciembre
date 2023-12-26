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
                    
                    bat "docker push diegoi3131/ms-test"
                }  
            }
        }
        
        stage('Deploy EKS') {
            steps {withCredentials([usernamePassword(credentialsId: 'azureServicePrincipal', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
                 bat "az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant e7960f8d-86e8-4c79-84ab-2e295180999e"
                    def resourceGroup = 'Devops'
                    def containerName = 'mycontainerinstance'

                    bat "az container create --resource-group ${resourceGroup} --name ${containerName} --image diegoi3131/ms-test --cpu 1 --memory 1.5 --restart-policy OnFailure"
            }
        }
    }
}
