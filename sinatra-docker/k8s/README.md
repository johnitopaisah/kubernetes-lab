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

## 💠 Tech Stack

- Ruby + Sinatra + Puma
- Docker
- AWS ECR Public
- Kubernetes (Minikube & Kind)
- kubectl, YAML
- Kubernetes Dashboard

---

## 🚧 Step-by-Step Setup

### 1. Build Sinatra App

**`server.rb`**:
```ruby
require 'sinatra'

get '/' do
  "Hello, world!"
end
```

**`Gemfile`**:
```ruby
source 'https://rubygems.org'

gem 'sinatra'
gem 'puma'
gem 'rack'
gem 'rackup'
```

**`config.ru`**:
```ruby
require './server'
run Sinatra::Application
```

---

### 2. Dockerize the App

**`Dockerfile`**:
```dockerfile
FROM ruby:3.1.0

WORKDIR /app

COPY Gemfile Gemfile.lock ./
RUN gem install bundler && \
    bundle config set --local path 'vendor/bundle' && \
    bundle install

COPY . .

EXPOSE 4567

CMD ["bundle", "exec", "rackup", "--host", "0.0.0.0", "--port", "4567"]
```

Build the Docker image:
```bash
docker build -t sinatra-example .
```

---

### 3. Push Image to AWS ECR

> Your ECR Public URL:  
`public.ecr.aws/z9j0k7n8/sinatra-example:latest`

```bash
docker tag sinatra-example public.ecr.aws/z9j0k7n8/sinatra-example:latest
docker push public.ecr.aws/z9j0k7n8/sinatra-example:latest
```

---

### 4. Deploy to Kubernetes

**`k8s/deployment.yaml`**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sinatra
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sinatra
  template:
    metadata:
      labels:
        app: sinatra
    spec:
      containers:
      - name: sinatra
        image: public.ecr.aws/z9j0k7n8/sinatra-example:latest
        ports:
        - containerPort: 4567
```

Apply the deployment:
```bash
kubectl apply -f k8s/deployment.yaml
```

---

### 5. Expose the App

**`k8s/service.yaml`**:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: sinatra-service
spec:
  type: NodePort
  selector:
    app: sinatra
  ports:
    - port: 4567
      targetPort: 4567
      nodePort: 30007
```

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
# Example output: 192.168.64.2
```
Then go to:
```
http://192.168.64.2:30007
```

---

### 7. Kubernetes Dashboard (Minikube & Kind)

#### 📦 For Minikube:
```bash
minikube dashboard --url
```

#### ⚙️ For Kind:
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

# Create admin user
kubectl apply -f admin-user.yaml

# Get token
kubectl -n kubernetes-dashboard create token admin-user

# Port forward
kubectl port-forward -n kubernetes-dashboard svc/kubernetes-dashboard 8080:443
```
Access: [https://localhost:8080](https://localhost:8080)

---

## 🚑 Troubleshooting

- **ImagePullBackOff**: Make sure your image is public and correctly tagged.
- **rackup not found**: Add `rack` and `rackup` to Gemfile, and run `bundle install`.
- **Pod not ready**: Check readinessProbe or container port mismatch.
- **Dashboard issues in Kind**: Use port-forward instead of `minikube dashboard`.

---

## 📸 Screenshots

| Description             | Screenshot                       |
|-------------------------|----------------------------------|
| App running in browser | Hello, world! via localhost:8080 |
| Kubernetes Dashboard   | Workload overview and logs       |
| ECR push success       | Terminal output showing push     |

*(Add screenshots manually or using markdown image syntax)*

---

## 👤 Author

**John Itopa ISAH**  
_MSc Embedded Software Systems Engineer, ISEP Paris_  
🔗 [LinkedIn](https://www.linkedin.com/in/johnitopaisah) | [GitHub](https://github.com/johnitopaisah) | [Medium Blog](#)

---

## 📜 License

MIT – Use, share, learn!
