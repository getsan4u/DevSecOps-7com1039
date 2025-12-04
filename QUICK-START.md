# DevSecOps Quick Start Guide

## 🚀 Getting Started

### Prerequisites Checklist
- [ ] Java 11+ installed
- [ ] Maven 3.6+ installed
- [ ] Docker installed and running
- [ ] kubectl configured
- [ ] Kubernetes cluster access (Minikube/Cloud)
- [ ] GitHub account with repository access

## 🔧 Local Development

### Build and Run Locally
```bash
# Clone repository
git clone <repository-url>
cd <repository-name>

# Build with Maven
mvn clean package

# Run application
java -jar target/spring-boot-web.jar

# Access application
open http://localhost:8080
```

### Run with Docker
```bash
# Build Docker image
docker build -t spring-boot-devsecops:local .

# Run container
docker run -p 8080:8080 spring-boot-devsecops:local

# Test
curl http://localhost:8080
```

## 🔐 Security Setup

### Configure GitHub Secrets

Navigate to: `Settings → Secrets and variables → Actions → New repository secret`

Required secrets:
```
SONAR_TOKEN          # From SonarCloud dashboard
SNYK_TOKEN           # From Snyk account settings
DOCKERHUB_USERNAME   # Your DockerHub username
DOCKERHUB_TOKEN      # DockerHub access token (not password)
GIT_TOKEN            # GitHub Personal Access Token
```

### SonarCloud Setup
1. Go to https://sonarcloud.io
2. Sign in with GitHub
3. Import your repository
4. Copy the project key
5. Generate token: `My Account → Security → Generate Token`
6. Add token to GitHub secrets as `SONAR_TOKEN`

### Snyk Setup
1. Go to https://snyk.io
2. Sign in with GitHub
3. Navigate to `Account Settings → API Token`
4. Copy token
5. Add to GitHub secrets as `SNYK_TOKEN`

### DockerHub Setup
1. Go to https://hub.docker.com
2. Navigate to `Account Settings → Security → New Access Token`
3. Create token with read/write permissions
4. Add username and token to GitHub secrets

## ☸️ Kubernetes Deployment

### Deploy to Minikube
```bash
# Start Minikube
minikube start

# Apply manifests
kubectl apply -f application-config/deployment.yml
kubectl apply -f application-config/service.yml

# Check deployment
kubectl get pods
kubectl get svc

# Get service URL
minikube service spring-boot-app-service --url

# View logs
kubectl logs -f deployment/spring-boot-app
```

### Deploy Hardened Version
```bash
# Use security-hardened manifests
kubectl apply -f application-config/deployment-hardened.yml
kubectl apply -f application-config/service.yml
```

## 🔄 CI/CD Pipeline

### Trigger Pipeline
```bash
# Make a change and push
git add .
git commit -m "Your change"
git push origin main
```

### Monitor Pipeline
1. Go to GitHub repository
2. Click `Actions` tab
3. View running workflow
4. Check each stage status

### View Security Reports

**SonarCloud (SAST)**
- Visit: https://sonarcloud.io
- Select your project
- View code quality and security issues

**Snyk (SCA)**
- Go to repository `Security` tab
- Click `Code scanning alerts`
- View dependency vulnerabilities

**OWASP ZAP (DAST)**
- Go to GitHub Actions workflow run
- Download artifacts
- Review ZAP scan report

## 🛠️ Common Tasks

### Update Application Code
```bash
# Edit source files
vim src/main/java/com/sanjid/StartApplication.java

# Test locally
mvn clean test

# Build and verify
mvn clean package

# Commit and push (triggers pipeline)
git add .
git commit -m "Update application"
git push
```

### Update Dependencies
```bash
# Check for updates
mvn versions:display-dependency-updates

# Update specific dependency in pom.xml
vim pom.xml

# Verify build
mvn clean verify

# Push changes
git add pom.xml
git commit -m "Update dependencies"
git push
```

### Update Docker Image
```bash
# Modify Dockerfile
vim Dockerfile

# Build locally to test
docker build -t test:local .
docker run -p 8080:8080 test:local

# Push changes (pipeline will build and push)
git add Dockerfile
git commit -m "Update Docker configuration"
git push
```

### Scale Deployment
```bash
# Scale to 3 replicas
kubectl scale deployment spring-boot-app --replicas=3

# Verify
kubectl get pods

# Or update deployment.yml and apply
vim application-config/deployment.yml
kubectl apply -f application-config/deployment.yml
```

## 🐛 Troubleshooting

### Pipeline Failures

**SAST Scan Fails**
```bash
# Check SonarCloud dashboard for issues
# Fix code quality issues locally
mvn sonar:sonar -Dsonar.login=<your-token>
```

**SCA Scan Fails**
```bash
# Check for vulnerable dependencies
mvn dependency:tree
# Update vulnerable dependencies in pom.xml
```

**Docker Build Fails**
```bash
# Test build locally
docker build -t test:local .
# Check logs for errors
# Verify artifact exists
ls -la target/
```

### Kubernetes Issues

**Pods Not Starting**
```bash
# Check pod status
kubectl describe pod <pod-name>

# View logs
kubectl logs <pod-name>

# Check events
kubectl get events --sort-by='.lastTimestamp'
```

**Service Not Accessible**
```bash
# Check service
kubectl get svc spring-boot-app-service

# Check endpoints
kubectl get endpoints spring-boot-app-service

# Port forward for testing
kubectl port-forward svc/spring-boot-app-service 8080:80
```

**Image Pull Errors**
```bash
# Verify image exists in DockerHub
docker pull sanjid369/devsecops21073983:latest

# Check image pull secrets if using private registry
kubectl get secrets
```

## 📊 Monitoring

### Check Application Health
```bash
# Health endpoint
curl http://<service-url>/actuator/health

# Application info
curl http://<service-url>/actuator/info
```

### View Logs
```bash
# All pods
kubectl logs -l app=spring-boot-app

# Follow logs
kubectl logs -f deployment/spring-boot-app

# Previous container logs
kubectl logs <pod-name> --previous
```

### Resource Usage
```bash
# Pod resource usage
kubectl top pods

# Node resource usage
kubectl top nodes
```

## 🔒 Security Best Practices

### Code Security
- Run SAST scan before committing: `mvn sonar:sonar`
- Check dependencies: `mvn dependency:tree`
- Review security hotspots in SonarCloud

### Container Security
- Use minimal base images
- Run as non-root user
- Scan images regularly
- Keep base images updated

### Kubernetes Security
- Use resource limits
- Enable security contexts
- Implement network policies
- Use secrets for sensitive data
- Enable RBAC

## 📚 Additional Resources

- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [SonarCloud Docs](https://docs.sonarcloud.io/)
- [Snyk Documentation](https://docs.snyk.io/)
- [OWASP ZAP Guide](https://www.zaproxy.org/docs/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)

## 🆘 Getting Help

- Check [ARCHITECTURE.md](./ARCHITECTURE.md) for system design
- Review [DEVSECOPS-ANALYSIS.md](./DEVSECOPS-ANALYSIS.md) for recommendations
- See [SECURITY.md](./SECURITY.md) for security policies
- Open GitHub issue for bugs or questions
