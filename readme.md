## Overview

This project demostrates deploying the InfraStore application in kubernetes cluster ( i have used minikube for execution ). It is designed to help you get hands-on experience with CI/CD automation, Kubernetes deployment with security best practices.

## Project Goals

- Deploy InfraStore as scable application using kubernetes.
- Use persistent storage where needed
- Use best security practices
- Automate deployment with Kubernetes manifests or Helm
- Include clear documentation for your setup

## InfraStore has 4 endpoints which is exposed with REST API:

### Method             Endpoint                  Description
- POST               /api/token/                Obtain a token for authentication
- POST 				/api/upload/ 			 	Upload a file
- GET 				/api/files/ 				List files currently in storage
- DELETE 			/api/files/<id>/ 			Delete a file from storage

## Example API usage:

curl -X POST http://localhost:8000/api/token/ \
-d "username=admin&password=XXXX"
	
curl -X POST http://localhost:8000/api/upload/ \
-H "Authorization: Token YOUR_TOKEN" \
-F "file=@example.txt"

curl -H "Authorization: Token YOUR_TOKEN" \
http://localhost:8000/api/files/

curl -X DELETE http://localhost:8000/api/files/1/ \
-H "Authorization: Token YOUR_TOKEN"

## Prerequisites:
  
## To install minikube:
  - curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  - chmod +x minikube-linux-amd64
  - sudo mv minikube-linux-amd64 /usr/local/bin/minikubechmod +x minikube-linux-amd64
  - minikube version
  - minikube start

## To install Argocd:
  - kubectl create namespace argocd
  - kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
  - kubectl get pods -n argocd
  - kubectl port-forward svc/argocd-server -n argocd 8080:443
  - kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 --decode
  
## helm template:
  We can use helm create infrastore-chart command or we can use github workflow to template manifest files.

## CI/CD pipeline steps:

 We have below branching strategy along with pipeline.
  - Feature CICD pipeline which builds and deploys to Dev & QA Enviornments with different namespaces.[FEATURE_RELEASE](https://github.com/RamprasadDamodar/new_project/blob/main/.github/workflows/feature-deploy.yaml)
  - Release tag pipeline which creates a release image tag and deploys on UAT Enviornment. [RELEASE_TAG](https://github.com/RamprasadDamodar/new_project/blob/main/.github/workflows/Release-tag-deploy.yaml)
  - Use promote/hotfix pipeline to deploy your application to PROD Environment. [HOTFIX](https://github.com/RamprasadDamodar/new_project/blob/main/.github/workflows/CD.yaml)


## CI/CD Architecture.
    
<img width="1608" height="334" alt="image" src="https://github.com/user-attachments/assets/769cb3a4-d450-412d-9ba9-a004f1b0af99" />

  # Pipeline Stages
  # The CI/CD pipeline consists of the following stages:

    - Unit Testing - Runs the test suite using Vitest
	- Static Code Analysis - Performs linting with ESLint
	- Build - Creates a production build of the application
	- Docker Image Creation - Builds a Docker image using a multi-stage Dockerfile
	- Docker Image Scan - Scans the image for vulnerabilities using Trivy
	- Docker Image Push - Pushes the image to GitHub Container Registry
	- Update Kubernetes value file - Updates the vaules.yaml file with the new image tag.

    
## Deployment Architecture

<img width="1436" height="712" alt="image" src="https://github.com/user-attachments/assets/a447c189-3a0c-44d4-a51c-8a1b9c1fe550" />


    
##  ArgoCD deployment strategy
1. After pushing changes to your repository on dev branch, GitHub Actions will automatically trigger the workflow.
2. We have used Argocd for CD purpose with argocd Application manifest.
     This contains the app of apps.yaml file which contains application yaml files of all the services which is deployed in kubernetes cluster. In our case we have used the same repo ref -  [app-of-apps](https://github.com/RamprasadDamodar/new_project/tree/main/apps)

# Note: Ingress object i have not created since i deployed in minikube cluster so Ingress flag i have set to False in helm chart.
	 

 3. This application yaml contains the path to our helm charts manifest which has all the k8s mainifest files to deploy into our cluster.
 4. Argocd uses a concept called gitops which continuously monitor our Github repo for any changes and auto-deploys if there is any changes.
 5. We have deployed argocd onto our minikube cluster where application is deployed.   

