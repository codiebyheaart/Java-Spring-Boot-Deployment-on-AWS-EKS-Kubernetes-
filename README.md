# Java Spring Boot Deployment on AWS EKS (Kubernetes)

This project documents the complete step-by-step process of deploying a **Java Spring Boot application** 
on **AWS Kubernetes (EKS)** using Docker and kubectl.

The goal was to learn Kubernetes in a real cloud environment and deploy an application publicly.

---

## 🔹 WHAT WAS ACHIEVED

  Created AWS EKS Cluster (Standard mode)  
  Created EC2 Node Group  
  Configured kubectl  
  Built Docker image using existing Dockerfile  
  Pushed image to DockerHub (ECR blocked by org policy)  
  Deployed Java Spring Boot into Kubernetes  
  Exposed app using AWS LoadBalancer  
  Verified application through browser  
  Checked logs and health  
  Managed pods and services

---
GitHub Repo → Docker Build → DockerHub → AWS EKS → EC2 Node → Kubernetes Deployment → LoadBalancer → Browser

## 🏗 INFRASTRUCTURE ARCHITECTURE

---

## 🧱 TECHNOLOGIES USED

- Java Spring Boot  
- Docker  
- GitHub  
- DockerHub  
- AWS EKS  
- Kubernetes (kubectl)  
- AWS EC2  
- AWS Load Balancer  

---

## ⚙ DEPLOYMENT STEPS (COMMANDS USED)

---

### 1️⃣ Connect Kubernetes Cluster

```bash
aws eks update-kubeconfig --region us-east-1 --name demo-eks-standard   // connect kubectl to AWS cluster
kubectl get nodes                                                        // verify worker node is ready

2 Build Docker Image from Dockerfile
docker build -t springboot-app .      // build image using project Dockerfile
docker images                         // list locally built images

3 . Login to DockerHub
docker login                          // authenticate to DockerHub

4.Tag & Push Image
docker tag springboot-app pankaj8900/springboot-app:latest   // rename image for DockerHub
docker push pankaj8900/springboot-app:latest                 // push image to DockerHub registry

5. 5️⃣ Create Deployment Manifests
nano java-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springboot-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: springboot-app
  template:
    metadata:
      labels:
        app: springboot-app
    spec:
      containers:
      - name: springboot-app
        image: pankaj8900/springboot-app:latest
        ports:
        - containerPort: 8080
6. Create Service
nano service.yaml

apiVersion: v1
kind: Service
metadata:
  name: springboot-service
spec:
  type: LoadBalancer
  selector:
    app: springboot-app
  ports:
  - port: 80
    targetPort: 8080

7️⃣ Deploy into Kubernetes
kubectl apply -f java-deployment.yaml     // deploy Spring Boot application
kubectl apply -f java-service.yaml        // expose service publicly

ACCESS APPLICATION
kubectl get svc    // fetch AWS load balancer URL


Then open:

http://<EXTERNAL-LB-DNS>


MONITOR & DEBUG
kubectl get pods                               // check running pods
kubectl logs deployment/springboot-app         // view application logs
kubectl describe pod <pod-name>                // debug pod state


SCALE APPLICATION
kubectl scale deployment springboot-app --replicas=3   // increase number of pods
kubectl scale deployment springboot-app --replicas=1   // scale down



CLEANUP (COST SAVING)

After testing, resources should be removed:

kubectl delete deployment springboot-app
kubectl delete svc springboot-service


## FUTURE IMPROVEMENTS ########
***Next areas of learning:
Helm deployment
CI/CD pipeline
Ingress controller
Auto-scaling
Monitoring (Prometheus & Grafana)
AWS ECR & IAM

# ✅ YOU ARE DONE ✅  
This README is professional and interview-ready.

AUTHOR
Pankaj Verma
Cloud & Kubernetes Learner
