# 🚀 VProfile Application – Complete DevOps Implementation

## 📌 Project Overview

This project demonstrates a **complete end-to-end DevOps lifecycle** for the VProfile application.

It includes:

* Application build using Java
* Docker containerization
* Multi-container orchestration (Docker Compose)
* Kubernetes deployment (production style)
* Secrets & Persistent storage
* Ingress for external access
* AWS EKS deployment

---

## 🏗️ Project Structure

```
.
├── ansible/
├── compose/
├── Docker-files/
├── kube-menifest/
├── src/
├── pom.xml
```

---

## ⚙️ Technologies Used

### 💻 Application Stack

* Java (Spring MVC)
* JSP / Servlet
* Apache Tomcat

### 🗄️ Database & Messaging

* MySQL
* Memcached
* RabbitMQ

### ⚙️ DevOps Tools

* Docker
* Docker Compose
* Kubernetes
* Ansible

### ☁️ Cloud

* AWS EKS

### 🌐 Networking

* Ingress
* NGINX Ingress Controller
* AWS Load Balancer Controller

---

## 🐳 Docker Setup

### Build Images

```
docker build -t vprofile-app ./Docker-files/app
docker build -t vprofile-db ./Docker-files/db
docker build -t vprofile-web ./Docker-files/web
```

### Run with Docker Compose

```
docker-compose up -d
```

---

## ☸️ Kubernetes Cluster Setup (Minikube)

### Start Cluster

```
minikube start
```

### Verify

```
kubectl get nodes
```

---

## 🌐 Install Ingress Controller (Minikube)

```
minikube addons enable ingress
```

Verify:

```
kubectl get pods -n ingress-nginx
```

---

## 📦 Kubernetes Deployment

### Step 1: Create Namespace

```
kubectl apply -f kube-menifest/namespce.yml
```

---

### Step 2: Create Secrets

```
kubectl apply -f kube-menifest/secret.yml
```

---

### Step 3: Deploy Database (StatefulSet)

```
kubectl apply -f kube-menifest/db/db-stateful.yml
```

---

### Step 4: Deploy Supporting Services

```
kubectl apply -f kube-menifest/memcahe/
kubectl apply -f kube-menifest/rabbitmq/
```

---

### Step 5: Deploy Application

```
kubectl apply -f kube-menifest/app/
kubectl apply -f kube-menifest/web/
```

---

## 🌐 Ingress Configuration (Minikube)

### ingress.yml

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: vprofile-ingress
  namespace: prod
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: vprofile.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

Apply:

```
kubectl apply -f ingress.yml
```

---

### Access Application

```
minikube ip
```

Update hosts:

```
sudo nano /etc/hosts
```

Add:

```
<MINIKUBE-IP> vprofile.local
```

Open:

```
http://vprofile.local
```

---

## ☁️ AWS EKS Cluster Setup (Production)

### Install eksctl

```
curl --silent --location \
"https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin
```

---

### Configure AWS

```
aws configure
```

---

### Create Cluster

```
eksctl create cluster \
  --name vprofile-cluster \
  --region ap-south-1 \
  --nodegroup-name workers \
  --node-type t3.medium \
  --nodes 2
```

---

### Verify Cluster

```
kubectl get nodes
```

---

## 🌐 Install AWS Load Balancer Controller

```
eksctl utils associate-iam-oidc-provider \
  --region ap-south-1 \
  --cluster vprofile-cluster \
  --approve
```

```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```

```
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

```
eksctl create iamserviceaccount \
  --cluster vprofile-cluster \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::<ACCOUNT-ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

```
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=vprofile-cluster
```

---

## 🌍 Ingress (AWS ALB)

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: vprofile-ingress
  namespace: prod
  annotations:
    kubernetes.io/ingress.class: alb
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

---

## 🔍 Verification

```
kubectl get pods -n prod
kubectl get svc -n prod
kubectl get pvc -n prod
kubectl get ingress -n prod
```

---

## 🔐 Secrets Management

* MYSQL_ROOT_PASSWORD
* MYSQL_DATABASE

---

## 💾 Persistent Storage

* MySQL uses StatefulSet + PVC
* Data is persistent

---

## 🚀 DevOps Workflow

```
Code → Build → Docker → Push → Kubernetes → Ingress → Production
```

---

## 🔥 Production Best Practices

* Use StatefulSet for DB
* Use Secrets (no hardcoding)
* Use Ingress for access
* Use CI/CD (Jenkins)
* Prefer managed DB (RDS) in production

---

## 📌 Future Enhancements

* Jenkins CI/CD pipeline
* Helm charts
* HTTPS (TLS)
* AWS RDS integration

---

## 👨‍💻 Author

Tawfeeq Ahmed
DevOps Engineer
