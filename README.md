# 🔐 DevSecOps Automated Pipeline

A complete DevSecOps CI/CD pipeline integrating security testing at every stage of the development lifecycle.

## 🏗️ Architecture

```
Git Push → Jenkins → Dependency-Check → SonarQube → Build → Docker → Trivy → Deploy → OWASP ZAP
```

## 🛠️ Tools Used

| Tool | Role | Type |
|------|------|------|
| **Jenkins** | CI/CD Orchestration | Automation |
| **OWASP Dependency-Check** | Dependency vulnerability scanning | SCA |
| **SonarQube** | Static code analysis | SAST |
| **Docker** | Containerization | Build |
| **Trivy** | Container image scanning | Image Scan |
| **OWASP ZAP** | Dynamic application security testing | DAST |

## 📋 Pipeline Stages

1. **Git Checkout** — Clone source code from GitHub
2. **Compile** — Build project with Maven
3. **OWASP Scan (SCA)** — Scan dependencies for known CVEs
4. **SonarQube Analysis (SAST)** — Static code quality and security analysis
5. **Build** — Generate JAR artifact with Maven
6. **Docker Build & Push** — Build Docker image and push to Docker Hub
7. **Trivy Scan** — Scan Docker image for HIGH/CRITICAL vulnerabilities
8. **Deploy** — Run application as Docker container
9. **OWASP ZAP (DAST)** — Dynamic security testing on running application

## 🚀 Getting Started

### Prerequisites

- Docker & Docker Compose
- Git

### Installation

```bash
# Clone the repository
git clone https://github.com/hajarelmaimouni/DevSecOps-Automated-Pipeline.git
cd DevSecOps-Automated-Pipeline

# Start Jenkins and SonarQube
docker-compose up -d

# Check containers
docker ps
```

### Access Services

| Service | URL | Default Credentials |
|---------|-----|---------------------|
| Jenkins | http://localhost:8080 | admin / (initial password) |
| SonarQube | http://localhost:9000 | admin / admin |

### Get Jenkins Initial Password

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

## ⚙️ Configuration

### Jenkins Plugins Required

- OWASP Dependency-Check
- SonarQube Scanner
- Docker Pipeline
- Pipeline API

### Tools Configuration (Manage Jenkins → Tools)

| Tool | Name | Version |
|------|------|---------|
| JDK | jdk1 | Auto-install |
| Maven | maven1 | 3.9.9 |
| SonarQube Scanner | sonarqube | Latest |
| Dependency-Check | owasp-dc | Latest |

## 📊 Results

### SonarQube Analysis
- **Quality Gate**: ✅ Passed
- **Security**: C (2 issues)
- **Reliability**: C (10 bugs)
- **Maintainability**: A (17 code smells)

### Trivy Image Scan
- **Alpine OS**: 58 vulnerabilities (HIGH: 43, CRITICAL: 15)
- **JAR dependencies**: 49 vulnerabilities (HIGH: 37, CRITICAL: 12)

### OWASP ZAP DAST
- Automated baseline scan on running container
- Report archived as HTML artifact in Jenkins

## 📁 Project Structure

```
DevSecOps-Automated-Pipeline/
├── docker-compose.yml      # Jenkins + SonarQube setup
├── Jenkinsfile             # Complete CI/CD pipeline script
└── README.md               # This file
```

## 🔧 Pipeline Script Overview

```groovy
pipeline {
    agent any
    tools {
        jdk 'jdk1'
        maven 'maven1'
    }
    stages {
        stage('Git Checkout') { ... }
        stage('Compile') { ... }
        stage('OWASP Scan') { ... }
        stage('SonarQube Analysis') { ... }
        stage('Build') { ... }
        stage('Build & Push Docker Image') { ... }
        stage('Trivy Scan') { ... }
        stage('Run Docker Container') { ... }
        stage('OWASP ZAP - DAST') { ... }
    }
}
```

## 📚 References

- [OWASP DevSecOps Guideline](https://owasp.org/www-project-devsecops-guideline/)
- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [SonarQube Documentation](https://docs.sonarqube.org/)
- [Trivy Documentation](https://trivy.dev/)
- [OWASP ZAP Documentation](https://www.zaproxy.org/docs/)

## 👩‍💻 Author

**Hajar El Maimouni**  
Lab: Sécurité des Applications — Prof. Yassine MALEH

---
⭐ If you found this useful, please give it a star!
