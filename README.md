# Savannah Informatics DevOps Assessment

**Candidate:** Secsiren
**Date:** March 2026
**Repository:** https://github.com/Secsiren/savannah-devops

---

## Overview

This repository contains a complete DevOps assessment implementation covering version control, containerization, infrastructure as code, CI/CD pipelines, and site reliability engineering.

---

## Task 1: Version Control Integration

### Approach
Created a Python Flask web application with a health check endpoint and pushed it to GitHub with meaningful commit history.

### Application Structure
```
savannah-devops/
├── app/
│   ├── app.py                  # Flask application
│   ├── requirements.txt        # Python dependencies
│   └── templates/
│       └── index.html          # Web UI
├── Dockerfile                  # Container definition
├── docker-compose.yml          # Multi-container setup
├── terraform/                  # IaC scripts
├── monitoring/                 # SRE configuration
└── ansible/                    # Configuration management
```

### Key Features
- Flask web app running on port 5000
- `/health` endpoint returning `{"status": "healthy"}` for monitoring
- Environment-aware configuration via `APP_ENV` variable

### Commit History
All commits follow conventional commit format: `feat:`, `chore:`, `fix:`

---

## Task 2: Containerization with Docker

### Approach
Containerized the Flask application using Docker with a multi-stage build approach. Used Docker Compose to simulate a multi-container environment with PostgreSQL.

### Dockerfile
- Base image: `python:3.11-slim` for minimal footprint
- Production server: `gunicorn` with 2 workers
- Exposes port 5000

### Docker Compose Services
| Service | Image | Port |
|---------|-------|------|
| app | Custom Flask | 5000 |
| db | postgres:15-alpine | 5432 |

### Running Locally
```bash
docker compose up --build
# Visit http://localhost:5000
```

---

## Task 3: Infrastructure as Code (Terraform)

### Approach
Used Terraform with LocalStack to simulate AWS infrastructure locally, enabling cost-free development and testing.

### Resources Created
| Resource | Name | Description |
|----------|------|-------------|
| VPC | savannah-vpc | Private network (10.0.0.0/16) |
| Subnet | savannah-subnet | Public subnet (10.0.1.0/24) |
| Internet Gateway | savannah-igw | Internet connectivity |
| Security Group | savannah-sg | Allows HTTP/HTTPS/SSH |
| EC2 Instance | savannah-vm | t2.micro virtual machine |
| Elastic IP | savannah-eip | Static public IP address |

### Commands
```bash
cd terraform/
terraform init
terraform plan
terraform apply -auto-approve
```

### Challenges
Used LocalStack instead of real AWS to avoid costs. The provider configuration required special endpoints pointing to localhost:4566.

---

## Task 4: CI/CD Pipeline (GitHub Actions)

### Approach
Implemented a two-stage GitHub Actions pipeline that automatically builds, tags, and pushes the Docker image to DockerHub on every push to main.

### Pipeline Stages
```
Push to main
     ↓
Build & Push (build-and-push job)
  ├── Checkout code
  ├── Login to DockerHub
  └── Build and push image → bugsiren/savannah-devops:latest
     ↓
Deploy (deploy job)
  └── Deploy application
```

### Environment Variables & Secrets
| Secret | Description |
|--------|-------------|
| DOCKER_USERNAME | DockerHub username |
| DOCKER_PASSWORD | DockerHub password |

### DockerHub Image
```
bugsiren/savannah-devops:latest
```

---

## Task 5: Site Reliability Engineering (SRE)

### Approach
Implemented a full monitoring stack using Prometheus, Grafana, and Alertmanager running via Docker Compose.

### Monitoring Stack
| Tool | Port | Purpose |
|------|------|---------|
| Prometheus | 9090 | Metrics collection (scrapes every 15s) |
| Grafana | 3000 | Visualization dashboards |
| Alertmanager | 9093 | Alert routing and management |
| Node Exporter | 9100 | System metrics (CPU, RAM, Disk) |

### Alert Rules
Two critical alerts configured:

1. **AppDown** - Fires when app is unreachable for >1 minute (severity: critical)
2. **HighResponseTime** - Fires when response time exceeds 500ms for >2 minutes (severity: warning)

### Running Monitoring Stack
```bash
cd monitoring/
docker compose -f docker-compose.monitoring.yml up -d
# Grafana: http://localhost:3000 (admin/admin123)
# Prometheus: http://localhost:9090
# Alertmanager: http://localhost:9093
```

### Grafana Dashboard
Imported Node Exporter Full dashboard (ID: 1860) showing real-time CPU, memory, disk and network metrics.

### Incident Response Procedure
1. **Detection** - Alertmanager fires alert when threshold exceeded
2. **Notification** - Alert routed to on-call engineer
3. **Investigation** - Check Grafana dashboard for anomalies
4. **Resolution** - Restart service or scale resources
5. **Post-mortem** - Document root cause and prevention measures

---

## Bonus Task: Ansible Configuration Management

### Approach
Wrote an Ansible playbook that configures the server locally (using `ansible_connection=local`) to demonstrate configuration management principles.

### Playbook Tasks
| Task | Description |
|------|-------------|
| Create devops group | Creates Linux group for access control |
| Add user to group | Grants devops group membership |
| Copy config.txt | Copies to /opt/ with rw-rw---- (660) permissions |
| Install PostgreSQL | Latest version from apt |
| Start PostgreSQL | Enabled and running via systemd |
| Install Nginx | Web server installation |
| Configure Nginx | Reverse proxy to Flask app on port 5000 |
| Restart Nginx | Applies new configuration |

### File Permissions
```
-rw-rw---- 1 root devops 130 /opt/config.txt
```
Only root and devops group members can read/write the config file.

### Running the Playbook
```bash
cd ansible/
ansible-playbook -i inventory.ini playbook.yml
```

### Result
```
ok=11   changed=8    unreachable=0    failed=0    skipped=0
```

---

## Tools & Technologies Used

| Category | Tools |
|----------|-------|
| Language | Python, Flask |
| Containers | Docker, Docker Compose |
| IaC | Terraform, LocalStack |
| CI/CD | GitHub Actions |
| Registry | DockerHub |
| Monitoring | Prometheus, Grafana, Alertmanager |
| Config Mgmt | Ansible |
| OS | Ubuntu 24.04 LTS |
| Version Control | Git, GitHub |

---

## Repository Structure
```
savannah-devops/
├── .github/
│   └── workflows/
│       └── ci-cd.yml
├── app/
│   ├── app.py
│   ├── requirements.txt
│   └── templates/index.html
├── ansible/
│   ├── inventory.ini
│   ├── playbook.yml
│   └── config.txt
├── monitoring/
│   ├── docker-compose.monitoring.yml
│   ├── prometheus.yml
│   └── alert_rules.yml
├── terraform/
│   └── main.tf
├── Dockerfile
├── docker-compose.yml
└── README.md
```
