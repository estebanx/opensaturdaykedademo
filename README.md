
# THIS REPO contains all the files to run the demo that was show in the OpenSaturday of September 3,2022

This demo will configure a Kubernetes Cluster in Azure and configure it with KEDA with the purpose that scaled Azure DevOps Agents. Here the steps:

1) Create the build agent container from a Dockerfile. 
2) Run the build agent container inside a host machine. 
3) Run the build agent container using Azure Kubernetes Service (AKS). 
4) Scaling the agents based on the number of jobs in 'waiting' status.  


Follow the instructions here to cover the the steps 1, 2 and 3: https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops  

```bash
# create the container build agent
docker build -t opensaturdaycontainerregistry.azurecr.io/dockeragent:ubuntu-20.04 .

# run the container build agent in host machine
docker run -e AZP_URL=https://dev.azure.com/estebanx `
  -e AZP_TOKEN=<YOUR_PAT_TOKEN> `
  -e AZP_POOL=linuxkubernetes `
  opensaturdaycontainerregistry.azurecr.io/dockeragent:ubuntu-20.04

# convert agent variables value to base64 in order to be able to be saved in the deployment file
echo -n '' | openssl base64

# deploy a Deployment to run the container build agent in Kubernetes
kubectl apply -f ./k8s/deployment-agent.yaml

# deploy KEDA's scaledObject to scale out/in the build agents based on number of waiting jobs:
kubectl apply -f ./k8s/scaledObject-keda.yaml
