## Overview

This project demostrates deploying the InfraStore application in kubernetes cluster ( i have used minikube for execution ). It is designed to help you get hands-on experience with CI/CD automation, Kubernetes deployment with best security practices.

## Project Goals

- Deploy InfraStore as scable application using kubernetes.
- Use persistent storage where needed
- Use best security practices
- Automate deployment with Kubernetes manifests or Helm
- Include clear documentation for your setup

## InfraStore has 4 endpoints which is exposed with REST API:

- Method             Endpoint                  Description
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
  
To helm template:
  We can use helm create infrastore-chart command or we can use github workflow to template manifest files.

## CI/CD Architecture.
  
## Deployment Architecture

<img width="1436" height="712" alt="image" src="https://github.com/user-attachments/assets/a447c189-3a0c-44d4-a51c-8a1b9c1fe550" />


## Deploy the InfraStore application to Kubernetes using ArgoCD.


1. Lab Setup Using This Repo
Clone the Repository
git clone https://github.com/<your-username>/App-Development-To-Deployment.git
cd App-Development-To-Deployment
Prerequisites
Python 3
pip
Podman
Configure Python Virtual Environment
Create a virtual environment:

python3 -m venv venv
Activate the virtual environment:

source venv/bin/activate
2. Django Project Setup
Install Django:

pip install django
Start a new Django project:

django-admin startproject sampleapp .
Create a new Django app:

python3 manage.py startapp helloworldsite
Run the development server:

python3 manage.py runserver
Access the app at:
http://127.0.0.1:8000/

3. Containerize with Podman
Build the container image:

podman build -t sampleapp .
Run the container:

podman run -d -p 8000:8000 sampleapp
Access the app in your browser:
http://localhost:8000/

4. CI/CD and Deployment
Automate Container Image Build and Push with GitHub Actions
Configure GitHub Actions Workflow

Ensure your repository contains a workflow file in the .github/workflows directory.
This workflow should automate building your container image and pushing it to your container registry (e.g., Docker Hub or GitHub Container Registry).
For a real example, review the workflow YAML files in .github/workflows within your repository.
Review GitHub Actions Execution

After pushing changes to your repository, GitHub Actions will automatically trigger the workflow.
To review the execution:
Go to your repository on GitHub.
Click on the Actions tab at the top.
Select the latest workflow run to view logs, steps, and results.
Check for any errors or warnings and ensure the image was successfully built and pushed.
You can click into each step of the workflow to see detailed logs and troubleshoot any issues.
Deploy to Kubernetes with ArgoCD
Install ArgoCD

Follow the official documentation: ArgoCD Getting Started
Quick install steps:
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Access ArgoCD Web UI

For lab environments, use port-forwarding:
kubectl port-forward svc/argocd-server -n argocd 8080:443
Access the UI at: https://127.0.0.1:8080/
Login to ArgoCD

Retrieve the initial admin password:
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
Login with username admin and the decoded password.
Update ArgoCD Documentation

To update your deployment configuration, edit your ArgoCD application manifest (YAML file) to reference the new container image tag pushed by GitHub Actions.
Example snippet:
spec:
  source:
    repoURL: 'https://github.com/<your-username>/App-Development-To-Deployment.git'
    path: k8s
    targetRevision: HEAD
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: sampleapp
Commit and push changes to your repository. ArgoCD will detect updates and synchronize your application automatically.
Monitor Deployment

In the ArgoCD Web UI, check the status of your application.
Ensure the new image is deployed and the app is running as expected.
The last final steps access the application deployed on. K8s using portforwarding

For lab environments, use port-forwarding:
 kubectl port-forward svc/django-service 8081:80 -n django-app
