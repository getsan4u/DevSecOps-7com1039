# DevSecOps Spring Boot Application

[![SonarCloud](https://sonarcloud.io/images/project_badges/sonarcloud-black.svg)](https://sonarcloud.io/summary/new_code?id=sanjidsalam7com)

A production-ready Spring Boot application demonstrating comprehensive DevSecOps practices with automated security scanning integrated into the CI/CD pipeline.

## 🏗️ Architecture

See [ARCHITECTURE.md](./ARCHITECTURE.md) for detailed architecture diagram and component descriptions.

## 🔒 Security-First Approach

This project implements **shift-left security** - identifying and fixing vulnerabilities early in the SDLC rather than in production.

### Security Tools

| Tool | Type | Purpose |
|------|------|---------|
| **SonarCloud** | SAST | Static code analysis for code quality and security vulnerabilities |
| **Snyk** | SCA | Software composition analysis for dependency vulnerabilities |
| **OWASP ZAP** | DAST | Dynamic application security testing on running application |

## 🚀 CI/CD Pipeline

The automated pipeline executes on every push:

1. **SAST Scan** - SonarCloud analyzes source code for vulnerabilities and code smells
2. **SCA Scan** - Snyk checks dependencies for known CVEs
3. **Build & Package** - Maven compiles and packages the application
4. **Docker Build** - Creates containerized image
5. **Push to Registry** - Publishes image to DockerHub
6. **Deploy to K8s** - ArgoCD deploys to Kubernetes cluster
7. **DAST Scan** - OWASP ZAP tests the running application

## 🛠️ Technology Stack

- **Application**: Spring Boot 2.2.4, Java 11, Maven
- **CI/CD**: GitHub Actions
- **Security**: SonarCloud, Snyk, OWASP ZAP
- **Container**: Docker
- **Orchestration**: Kubernetes (Minikube for local)
- **GitOps**: ArgoCD
- **Registry**: DockerHub

## 📋 Prerequisites

- Java 11+
- Maven 3.6+
- Docker
- Kubernetes cluster (Minikube/EKS/GKE/AKS)
- ArgoCD installed on cluster

## 🏃 Quick Start

### Local Development

```bash
# Build the application
mvn clean package

# Run locally
java -jar target/spring-boot-web.jar

# Access at http://localhost:8080
```

### Docker

```bash
# Build image
docker build -t spring-boot-devsecops:latest .

# Run container
docker run -p 8080:8080 spring-boot-devsecops:latest
```

### Kubernetes Deployment

```bash
# Apply Kubernetes manifests
kubectl apply -f application-config/deployment.yml
kubectl apply -f application-config/service.yml

# Check deployment
kubectl get pods
kubectl get svc spring-boot-app-service
```

## 🔐 Security Configuration

### Required Secrets

Configure these secrets in GitHub repository settings:

- `SONAR_TOKEN` - SonarCloud authentication token
- `SNYK_TOKEN` - Snyk API token
- `DOCKERHUB_USERNAME` - DockerHub username
- `DOCKERHUB_TOKEN` - DockerHub access token
- `GIT_TOKEN` - GitHub personal access token

### Security Reports

- **SonarCloud**: View detailed SAST reports in SonarCloud dashboard
- **Snyk**: Check dependency vulnerabilities in GitHub Security tab
- **OWASP ZAP**: Download scan artifacts from GitHub Actions workflow runs

## 📊 Monitoring & Observability

- Application exposes health endpoints via Spring Boot Actuator
- Kubernetes deployment configured with 2 replicas for high availability
- Service exposed via NodePort for external access

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

All PRs automatically trigger security scans before merge.

## 📄 License

This project is open source and available for educational purposes.

## 📞 Support

For security vulnerabilities, please refer to [SECURITY.md](./SECURITY.md)


