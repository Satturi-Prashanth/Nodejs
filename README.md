# 🚀 My Java App — CI/CD Pipeline to AWS EKS

This project demonstrates a complete **CI/CD pipeline using Jenkins** to build, analyze, package, containerize, and deploy a **Java (Maven)** application into **AWS EKS (Kubernetes)**.

It covers all key DevOps stages — from **source code management** to **continuous deployment** using modern tools like Jenkins, SonarQube, Nexus, Docker, and Kubernetes.

---

## 🏗️ Project Overview

| Stage | Tool/Service | Description |
|--------|---------------|-------------|
| Source Control | Git & GitHub | Stores application code |
| Build | Maven | Compiles and packages the Java app |
| Code Quality | SonarQube | Scans for bugs, code smells, vulnerabilities |
| Artifact Repository | Nexus | Stores the built `.jar` file |
| Containerization | Docker | Builds and pushes Docker images |
| Image Repository | Docker Hub | Stores Docker images |
| Deployment | AWS EKS | Runs the application on Kubernetes |
| CI/CD Orchestration | Jenkins | Automates the entire pipeline |

---

## 📁 Folder Structure

