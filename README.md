# 🌐 DevOps HTML Webpage — CI/CD with Jenkins, Docker & Nexus

A DevOps project demonstrating automated build and delivery of a static HTML web application using Jenkins, Docker, and a **self-hosted Nexus artifact registry** — showcasing private image management as an alternative to cloud-based registries like ECR or DockerHub.

---

## 🏗️ Architecture Overview

```
Developer Push (GitHub)
        ↓
  Jenkins CI/CD Pipeline
        ↓
  Docker Build (from app/ directory)
        ↓
  Push to Nexus (Self-Hosted Docker Registry)
        ↓
  Container Run & Local Test (port 8080)
        ↓
  Ansible → Server Configuration
        ↓
  Kubernetes → Deployment & Scaling
```

---

## 🛠️ Tech Stack

| Category | Tool |
|---|---|
| Application | Static HTML/CSS webpage |
| CI/CD | Jenkins (Declarative Pipeline) |
| Containerization | Docker |
| Artifact Registry | **Nexus Repository Manager** (self-hosted) |
| Configuration Management | Ansible |
| Orchestration | Kubernetes |
| Version Control | GitHub (SSH authentication) |

---

## 🔑 Why Nexus?

Most projects use cloud registries (AWS ECR, DockerHub). This project uses **self-hosted Nexus Repository Manager** — a common choice in enterprise environments where:

- Images must stay on-premises or within a private network
- Teams need fine-grained access control over artifacts
- Organizations want to proxy and cache public Docker images internally
- Air-gapped or restricted cloud access environments are required

---

## 🚀 CI/CD Pipeline Stages

The Jenkins pipeline (`Jenkinsfile`) automates the following stages:

### Stage 1 — Checkout Code
Clones the `main` branch from GitHub using SSH key authentication configured in Jenkins credentials.

### Stage 2 — Build Docker Image
Builds the Docker image from the `app/` directory where the Dockerfile and HTML application live.
```
Image: devops-html-app:1.0
```

### Stage 3 — Login to Nexus
Authenticates to the self-hosted Nexus Docker registry at `172.31.21.36:8082` using credentials stored in Jenkins (`nexus-creds`).

### Stage 4 — Tag Image
Tags the locally built image for the Nexus registry:
```
172.31.21.36:8082/devops-html-app:1.0
```

### Stage 5 — Push to Nexus
Pushes the tagged image to the private Nexus Docker hosted repository.

### Stage 6 — Run Container (Local Test)
Runs the container locally to verify the application is serving correctly:
```
Container port 80 → Host port 8080
```

### Post Actions
- On success: logs confirmation message
- On failure: logs failure alert for debugging

---

## 📁 Project Structure

```
devops-html-webpage/
├── app/                   # HTML application + Dockerfile
├── ansible-project/       # Ansible playbooks for server config
├── k8s/                   # Kubernetes deployment manifests
├── Jenkinsfile            # 6-stage CI/CD pipeline (Nexus registry)
└── .gitignore
```

---

## 🐳 Docker — Manual Steps

```bash
# Build the image locally
cd app
docker build -t devops-html-app:1.0 .

# Run locally
docker run -d -p 8080:80 --name html-app devops-html-app:1.0

# Visit in browser
http://localhost:8080

# Stop and remove container
docker stop html-app && docker rm html-app
```

---

## 🗄️ Nexus Repository Setup

To replicate this project, Nexus must be running with a Docker hosted repository:

1. Install Nexus Repository Manager (OSS) on a server
2. Create a **Docker (hosted)** repository on port `8082`
3. Allow HTTP connector (for internal registries without SSL)
4. Add Nexus credentials to Jenkins as `nexus-creds` (username/password)
5. Configure Docker daemon to allow the insecure registry:

```json
# /etc/docker/daemon.json
{
  "insecure-registries": ["<nexus-server-ip>:8082"]
}
```

---

## ☸️ Kubernetes Deployment

Kubernetes manifests in `k8s/` can be applied to deploy the containerized HTML app on a cluster:

```bash
# Apply manifests
kubectl apply -f k8s/

# Check pods
kubectl get pods

# Get service URL
kubectl get svc
```

---

## ⚙️ Ansible Configuration

Ansible playbooks in `ansible-project/` automate server setup including Docker installation, Nexus configuration, and deployment tasks:

```bash
cd ansible-project
ansible-playbook -i inventory playbook.yml
```

---

## 🔒 Security Notes

- Jenkins credentials store used for Nexus login — no passwords in pipeline code
- GitHub SSH key authentication for secure repo access
- Nexus hosted repository keeps Docker images within a private network
- For production: enable HTTPS on Nexus and remove insecure-registry config

---

## 👨‍💻 Author

**Sandip** — Aspiring DevOps Engineer  
Transitioning from IT Admin & Network Support to Cloud & DevOps  
📍 Hyderabad, India

[![GitHub](https://img.shields.io/badge/GitHub-sforsandip-181717?logo=github)](https://github.com/sforsandip)

---

## 📌 Topics

`devops` `jenkins` `docker` `nexus` `kubernetes` `ansible` `ci-cd` `html` `docker-registry` `private-registry` `artifact-management`
