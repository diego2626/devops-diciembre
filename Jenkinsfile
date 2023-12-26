pipeline {
    agent any

    environment {
        RESOURCE_GROUP = 'Devops'
        CONTAINER_NAME = 'mycontainerinstance'
    }

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
        
        stage('Deploy CI') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azureServicePrincipal', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
                    bat "az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant e7960f8d-86e8-4c79-84ab-2e295180999e"
                    
                    // Eliminar el grupo de contenedores existente
                    bat "az container delete --resource-group ${env.RESOURCE_GROUP} --name ${env.CONTAINER_NAME} --yes"

                    // Crear el grupo de contenedores con los nuevos parámetros
                    bat "az container create --resource-group Devops --name mycontainerinstance --image diegoi3131/ms-test --cpu 1 --memory 1.5 --restart-policy OnFailure --ip-address public --ports 80 443"
                }
            }
        }
        stage('Deploy to AKS') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azureServicePrincipal', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {

                    bat "az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant e7960f8d-86e8-4c79-84ab-2e295180999e"

                    bat "az account set --subscription 1ad9edfe-5a09-4163-a6a8-fd82618d3985"

                    bat "az aks get-credentials --resource-group Devops --name myAKSCluster"
   
                    bat 'az aks get-credentials --resource-group Devops --name myAKSCluster --overwrite-existing'

                    bat 'kubectl apply -f k8s.yml'
                    
                    // Opcional: Exponer la aplicación si es necesario
                    // bat 'kubectl expose deployment my-container-deployment --type=LoadBalancer --name=my-service'

                    // Verificar el despliegue
                    bat 'kubectl get pods'
                }
            }
        }
    }
}
