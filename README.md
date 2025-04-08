# 🚀 Argo CD on Minikube

This repo shows how to install and use **Argo CD** on a **local Minikube cluster** to deploy applications using **Helm**.

---

## 🛠️ Prerequisites

Make sure the following are installed on your machine:

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)
- [Argo CD CLI](https://argo-cd.readthedocs.io/en/stable/cli_installation/)

---

## 🔧 Step 1: Start Minikube and Install Argo CD

```bash
minikube start --driver=docker

# Create namespace for Argo CD
kubectl create namespace argocd

# Install Argo CD core components
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


