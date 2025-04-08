# 🚀 Argo CD on Minikube - Complete GitOps Setup Guide

This guide provides end-to-end instructions for setting up Argo CD on a Minikube cluster, including all prerequisites, installation steps, and first application deployment.

## 🛠️ Prerequisites Installation

### 1. Install Minikube
```bash
# For Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### 2. Install kubectl
```bash
# Linux
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### 3. Install Helm (Optional)
```bash
# Linux/macOS
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

### 4. Start Minikube Cluster
```bash
minikube start --driver=docker --memory=4096 --cpus=2
minikube addons enable ingress
```

## 🔧 Argo CD Installation

### 1. Install Argo CD
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Install Argo CD CLI
```bash
# Linux
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```

## 🔐 Accessing Argo CD

### 1. Get Admin Password
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

### 2. Port Forwarding
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### 3. Login via CLI
```bash
argocd login localhost:8080 --username admin --password <YOUR_PASSWORD> --insecure
```

### 4. Access Web UI
Open in browser: https://localhost:8080
Username: admin
Password: <YOUR_PASSWORD>

## 🚀 Deploy Your First Application

### 1. Example Application
```bash
argocd app create guestbook \
  --repo https://github.com/argoproj/argocd-example-apps.git \
  --path guestbook \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

### 2. Sync Application
```bash
argocd app sync guestbook
```

### 3. Check Status
```bash
argocd app get guestbook
```

## 🌐 Alternative Access Methods

### 1. NodePort Access
```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
minikube service -n argocd argocd-server --url
```

### 2. Ingress (if enabled)
```yaml
echo '
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
spec:
  ingressClassName: nginx
  rules:
  - host: argocd.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argocd-server
            port:
              name: https
' | kubectl apply -f -
```

## 🧹 Cleanup
```bash
# Delete Argo CD
kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Delete namespace
kubectl delete namespace argocd

# Stop Minikube
minikube stop

# Delete cluster
minikube delete
```

This comprehensive guide includes all installation steps, access methods, deployment examples, and cleanup instructions for a complete Argo CD setup on Minikube.
