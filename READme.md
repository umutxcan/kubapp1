# RKE2 Kubernetes Cluster Project

## 📘 Overview
This project sets up a **high-availability Kubernetes cluster** (RKE2) with **3 master** and **2 worker nodes**.  
The infrastructure supports **continuous deployment**, **load balancing**, and **monitoring** for a database-backed web application.

---

## ⚙️ Architecture
- **Kubernetes (RKE2)** — 3 master, 2 worker nodes  
- **Helm** — application deployment management  
- **Jenkins** — CI/CD automation for seamless deploys  
- **NGINX Ingress** — public routing and SSL termination  
- **LoadBalancer** —  load balancing   
- **PostgreSQL Database** — external DB server (outside Kubernetes)  
- **Prometheus / Grafana** — cluster monitoring and visualization  
- **Alertmanager** — notification system for critical alerts  

---

## 🚀 Deployment Flow
1. Application code pushed to Git repository.  
2. Jenkins pipeline builds and pushes Docker images.  
3. Helm chart deployed automatically to Kubernetes cluster.  
4. NGINX Ingress exposes the application publicly.  
5. Prometheus scrapes metrics from Kubernetes and external DB server.  
6. Grafana visualizes metrics and Alertmanager sends alerts.  

---

## 🧩 Components
| Component | Purpose |
|------------|----------|
| RKE2 Cluster | Core control plane & workload nodes |
| Helm | Application packaging and deployment |
| Jenkins | CI/CD pipeline management |
| LoadBalancer | Load balancing and failover |
| PostgreSQL | External database for application |
| Prometheus | Metrics collection |
| Grafana | Visualization dashboards |
| Alertmanager | Alert notifications |

---


## 📁 Repository Structure

.
├── charts/
│   └── myapp/                 # Helm chart for application
├── jenkins/
│   └── Jenkinsfile            # CI/CD pipeline definition
├── monitoring/
│   ├── prometheus-values.yaml
│   ├── grafana-values.yaml
│   └── alertmanager-values.yaml
└── README.md

---

## 🧑‍💻 Author
Created by **[Umut Can]** — DevOps Automation & Cloud Infrastructure Project