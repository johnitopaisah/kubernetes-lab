# 🚀 Sinatra Docker + Kubernetes Deployment Lab

This project is a complete hands-on lab that walks through building, containerizing, and deploying a simple Ruby-based web application using **Docker**, **AWS ECR**, and **Kubernetes**. It also includes bonus steps for running the app in **Minikube**, **Kind**, and setting up the **Kubernetes Dashboard**.

---

## 📌 Table of Contents

- [Project Overview](#project-overview)
- [Tech Stack](#tech-stack)
- [Step-by-Step Setup](#step-by-step-setup)
  - [1. Build Sinatra App](#1-build-sinatra-app)
  - [2. Dockerize the App](#2-dockerize-the-app)
  - [3. Push Image to AWS ECR](#3-push-image-to-aws-ecr)
  - [4. Deploy to Kubernetes](#4-deploy-to-kubernetes)
  - [5. Expose the App](#5-expose-the-app)
  - [6. Access the App via Browser](#6-access-the-app-via-browser)
  - [7. Kubernetes Dashboard (Minikube & Kind)](#7-kubernetes-dashboard-minikube--kind)
- [Troubleshooting](#troubleshooting)
- [Screenshots](#screenshots)
- [Author](#author)

---

## 🧠 Project Overview

This lab demonstrates how to:

- Build a Sinatra web app that says "Hello, world!"
- Containerize it using Docker
- Push the image to [AWS ECR Public](https://gallery.ecr.aws/)
- Deploy the app using Kubernetes in both **Minikube** and **Kind**
- Access the app via browser or terminal
- Visualize everything using the Kubernetes Dashboard

---

## 🔠 Tech Stack

- Ruby + Sinatra + Puma
- Docker
- AWS ECR Public
- Kubernetes (Minikube & Kind)
- kubectl, YAML
- Kubernetes Dashboard

---

## 🚧 Step-by-Step Setup

### 1. Build Sinatra App
- App code: [`server.rb`](./server.rb)
- Dependencies: [`Gemfile`](./Gemfile), [`Gemfile.lock`](./Gemfile.lock)
- Rack entry point: [`config.ru`](./config.ru)

---

### 2. Dockerize the App
- Dockerfile: [`Dockerfile`](./Dockerfile)
- Build the image:
  ```bash
  docker build -t sinatra-example .
  ```

---

### 3. Push Image to AWS ECR
- Image URL: `public.ecr.aws/z9j0k7n8/sinatra-example:latest`
- Commands:
  ```bash
  docker tag sinatra-example public.ecr.aws/z9j0k7n8/sinatra-example:latest
  docker push public.ecr.aws/z9j0k7n8/sinatra-example:latest
  ```

---

### 4. Deploy to Kubernetes
- Deployment YAML: [`k8s/deployment.yaml`](./k8s/deployment.yaml)
  ```bash
  kubectl apply -f k8s/deployment.yaml
  ```

---

### 5. Expose the App
- Service YAML: [`k8s/service.yaml`](./k8s/service.yaml)
  ```bash
  kubectl apply -f k8s/service.yaml
  ```

---

### 6. Access the App via Browser

#### Option A: Port-Forward
```bash
kubectl port-forward deployment/sinatra --address 0.0.0.0 8080:4567
```
Visit:
```
http://localhost:8080
```

#### Option B: NodePort + Minikube
```bash
minikube ip
# Example: 192.168.64.2
```
Then go to:
```
http://192.168.64.2:30007
```

---

### 7. Kubernetes Dashboard (Minikube & Kind)

#### 📦 Minikube Dashboard
```bash
minikube dashboard --url
```

#### ⚙️ Kind Dashboard
- Apply dashboard manifests:
  ```bash
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
  kubectl apply -f admin-user.yaml
  ```
- Get access token:
  ```bash
  kubectl -n kubernetes-dashboard create token admin-user
  ```
- Port-forward:
  ```bash
  kubectl port-forward -n kubernetes-dashboard svc/kubernetes-dashboard 8080:443
  ```
- Visit: [https://localhost:8080](https://localhost:8080)

---

## 🚑 Troubleshooting

- **ImagePullBackOff**: Ensure your image is public and tagged correctly.
- **rackup not found**: Ensure `rack` and `rackup` are in your Gemfile and run `bundle install`.
- **Pod not ready**: Confirm readiness probes and exposed ports match.
- **Dashboard on Kind**: Use `port-forward` instead of `minikube dashboard`.

---

## 📸 Screenshots

| Description             | Screenshot                       |
|-------------------------|----------------------------------|
| App running in browser | Hello, world! via localhost:8080 |
| Kubernetes Dashboard   | Workload overview and logs       |
| ECR push success       | Terminal output showing push     |

*Add your screenshots to the repo as needed.*

---

## 👤 Author

**John Itopa ISAH**  
_MSc Embedded Software Systems Engineer, ISEP Paris_  
🔗 [LinkedIn](https://www.linkedin.com/in/johnitopaisah) | [GitHub](https://github.com/johnitopaisah) | [Medium Blog](#)

---

## 📜 License

MIT – Use, share, learn!
