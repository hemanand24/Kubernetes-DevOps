Kubernetes DevOps Hiring Assignment

Objective:

Deploy a "Hello World" application using NGINX on a local Kubernetes cluster (Kind, Minikube, or K3s).
Optionally integrate Argo CD for GitOps automation.
Record a video walkthrough demonstrating the entire setup, deployment, and access.


Project Structure 
- app/index.html  
- docker/Dockerfile  
- k8s/deployment.yaml, service.yaml  
- demo/video.mp4  
- README.md



Steps:

1. Create a Simple HTML Application

File: app/index.html

    <h1>Hello, World from Kubernetes!</h1>




2. Dockerize the App

Install Docker and Kind.

To Complete and run the project natively in Windows, Kind can be installed via Chocolatey in PowerShell(Run in adminstrator mode).
    
    PS C:\Users\heman> Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
    
    PS C:\Users\heman> choco install kind
    
Include the PATH variable for kind if not done automatically

File: docker/Dockerfile
    
    FROM nginx:alpine
    COPY app/index.html /usr/share/nginx/html/index.html


Build Docker Image:

    PS C:\Users\heman\Desktop\XivTech> docker build -t hello-world-nginx docker/Dockerfile .


Load Docker Image into Kind:

    PS C:\Users\heman\Desktop\XivTech> kind load docker-image hello-world-nginx




3. Set Up Local Kubernetes Cluster

Create Kubernetes Cluster:
    
    PS C:\Users\heman\Desktop\XivTech> kind create cluster --name hello-world-cluster


Verify Cluster:
    
    PS C:\Users\heman\Desktop\XivTech> kubectl get nodes





4. Kubernetes Deployment and Service

Files: k8s/ -deployment.yaml

Deployment YAML: defines an NGINX Pod using the custom Docker image.

Service YAML: exposes the deployment externally via NodePort (port 30007).

Apply Manifests:

    PS C:\Users\heman\Desktop\XivTech> kubectl apply -f k8s/

Verify Deployment and Service:
    
    PS C:\Users\heman\Desktop\XivTech> kubectl get pods
    NAME                                      READY   STATUS    RESTARTS   AGE
    hello-world-deployment-6954587469-9xtzk   1/1     Running   0          4h23m
    
    PS C:\Users\heman\Desktop\XivTech> kubectl get services
    NAME                  TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
    hello-world-service   NodePort    10.96.57.117   <none>        80:30007/TCP   3h40m
    kubernetes            ClusterIP   10.96.0.1      <none>        443/TCP        5h33m

Access the App:

If NodePort doesn't work directly (curl http://localhost:30007/ fails), use port-forwarding:
    
    PS C:\Users\heman\Desktop\XivTech> kubectl port-forward service/hello-world-service 8085:80

Then access it locally via:
    
    PS C:\Users\heman\Desktop\XivTech> curl http://localhost:8085/

or open http://localhost:8085 in a browser.




5. Push the project to GitHub repository
    
        $ git init
    
        $ git add .
    
        $ git commit -m "First commit"
    
        $ git remote add kubdev https://github.com/hemanand24/Kubernetes-DevOps.git
    
        $ git push kubdev master




6. (Optional) GitOps with Argo CD

Install Argo CD in the cluster.
    
    PS C:\Users\heman\Desktop\XivTech> kubectl create namespace argocd
    PS C:\Users\heman\Desktop\XivTech> kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Access Argo CD UI by port-forwarding:
    
    PS C:\Users\heman\Desktop\XivTech> kubectl port-forward svc/argocd-server -n argocd 9090:443

