# Architecture Overview

## System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              DEVELOPER WORKFLOW                              │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      │ git push
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              GITHUB REPOSITORY                               │
│                         (Source Code Management)                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      │ webhook trigger
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           GITHUB ACTIONS CI/CD                               │
│                                                                               │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STAGE 1: SAST (Static Application Security Testing)                │   │
│  │  ┌──────────────────────────────────────────────────────────────┐   │   │
│  │  │  SonarCloud                                                   │   │   │
│  │  │  • Code quality analysis                                      │   │   │
│  │  │  • Security vulnerability detection                           │   │   │
│  │  │  • Code smell identification                                  │   │   │
│  │  │  • Technical debt measurement                                 │   │   │
│  │  └──────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                      │                                        │
│                                      ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STAGE 2: SCA (Software Composition Analysis)                       │   │
│  │  ┌──────────────────────────────────────────────────────────────┐   │   │
│  │  │  Snyk                                                         │   │   │
│  │  │  • Dependency vulnerability scanning                          │   │   │
│  │  │  • License compliance checking                                │   │   │
│  │  │  • CVE detection in libraries                                 │   │   │
│  │  │  • SARIF report to GitHub Security                            │   │   │
│  │  └──────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                      │                                        │
│                                      ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STAGE 3: BUILD & PACKAGE                                           │   │
│  │  ┌──────────────────────────────────────────────────────────────┐   │   │
│  │  │  Maven Build                                                  │   │   │
│  │  │  • Compile Java source code                                   │   │   │
│  │  │  • Run unit tests                                             │   │   │
│  │  │  • Package as JAR (spring-boot-web.jar)                       │   │   │
│  │  └──────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                      │                                        │
│                                      ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STAGE 4: CONTAINERIZATION                                          │   │
│  │  ┌──────────────────────────────────────────────────────────────┐   │   │
│  │  │  Docker Build                                                 │   │   │
│  │  │  • Build image from Dockerfile                                │   │   │
│  │  │  • Base: adoptopenjdk/openjdk11:alpine-jre                    │   │   │
│  │  │  • Tag: sanjid369/devsecops21073983:latest                    │   │   │
│  │  └──────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                      │                                        │
│                                      ▼                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  STAGE 5: REGISTRY PUSH                                             │   │
│  │  ┌──────────────────────────────────────────────────────────────┐   │   │
│  │  │  DockerHub                                                    │   │   │
│  │  │  • Push container image                                       │   │   │
│  │  │  • Version tagging                                            │   │   │
│  │  └──────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                          KUBERNETES CLUSTER                                  │
│                                                                               │
│  ┌────────────────────────────────────────────────────────────────────┐     │
│  │  ArgoCD (GitOps Deployment)                                        │     │
│  │  • Monitors Git repository                                         │     │
│  │  • Automatic sync and deployment                                   │     │
│  │  • Declarative configuration                                       │     │
│  └────────────────────────────────────────────────────────────────────┘     │
│                                      │                                        │
│                                      ▼                                        │
│  ┌────────────────────────────────────────────────────────────────────┐     │
│  │  Deployment: spring-boot-app                                       │     │
│  │  ┌──────────────────────┐  ┌──────────────────────┐               │     │
│  │  │  Pod 1               │  │  Pod 2               │               │     │
│  │  │  ┌────────────────┐  │  │  ┌────────────────┐  │               │     │
│  │  │  │ Container      │  │  │  │ Container      │  │               │     │
│  │  │  │ Port: 8080     │  │  │  │ Port: 8080     │  │               │     │
│  │  │  └────────────────┘  │  │  └────────────────┘  │               │     │
│  │  └──────────────────────┘  └──────────────────────┘               │     │
│  │                                                                     │     │
│  │  Replicas: 2 (High Availability)                                   │     │
│  └────────────────────────────────────────────────────────────────────┘     │
│                                      │                                        │
│                                      ▼                                        │
│  ┌────────────────────────────────────────────────────────────────────┐     │
│  │  Service: spring-boot-app-service                                  │     │
│  │  • Type: NodePort                                                  │     │
│  │  • Port: 80 → TargetPort: 8080                                     │     │
│  │  • Load balances across pods                                       │     │
│  └────────────────────────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           DAST SECURITY SCAN                                 │
│  ┌────────────────────────────────────────────────────────────────────┐     │
│  │  OWASP ZAP (Dynamic Application Security Testing)                  │     │
│  │  • Scans running application                                       │     │
│  │  • Tests for runtime vulnerabilities                               │     │
│  │  • XSS, SQL injection, CSRF detection                              │     │
│  │  • Generates security report artifacts                             │     │
│  └────────────────────────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                              END USERS                                       │
│                    Access via NodePort/LoadBalancer                          │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Component Details

### Application Layer

**Spring Boot Application**
- Framework: Spring Boot 2.2.4
- Java Version: 11
- Build Tool: Maven
- Web Framework: Spring MVC with Thymeleaf templates
- Port: 8080

### Security Scanning Layers

#### 1. SAST - SonarCloud
- **Purpose**: Analyze source code for security vulnerabilities and code quality issues
- **Triggers**: On every push to repository
- **Checks**:
  - Security hotspots
  - Code vulnerabilities
  - Code smells
  - Technical debt
  - Code coverage
- **Integration**: Maven plugin with GitHub Actions

#### 2. SCA - Snyk
- **Purpose**: Scan dependencies for known vulnerabilities
- **Triggers**: After SAST scan completes
- **Checks**:
  - CVE detection in dependencies
  - License compliance
  - Outdated packages
- **Output**: SARIF report uploaded to GitHub Security tab

#### 3. DAST - OWASP ZAP
- **Purpose**: Test running application for runtime vulnerabilities
- **Triggers**: After deployment to Kubernetes
- **Checks**:
  - XSS (Cross-Site Scripting)
  - SQL Injection
  - CSRF (Cross-Site Request Forgery)
  - Security headers
  - SSL/TLS configuration
- **Output**: Scan artifacts stored in GitHub Actions

### Container Layer

**Docker**
- Base Image: `adoptopenjdk/openjdk11:alpine-jre`
- Optimized for size with Alpine Linux
- Single JAR deployment model
- Registry: DockerHub

### Orchestration Layer

**Kubernetes**
- **Deployment**: 2 replicas for high availability
- **Service**: NodePort type for external access
- **Port Mapping**: 80 (external) → 8080 (container)
- **Environment**: Minikube (local) or cloud providers

**ArgoCD**
- GitOps continuous delivery
- Automatic synchronization
- Declarative configuration
- Self-healing capabilities

## Data Flow

1. **Code Commit** → Developer pushes code to GitHub
2. **CI Trigger** → GitHub Actions workflow starts automatically
3. **Security Gates** → SAST and SCA scans must pass
4. **Build** → Maven compiles and packages application
5. **Containerize** → Docker builds image from artifact
6. **Publish** → Image pushed to DockerHub registry
7. **Deploy** → ArgoCD pulls image and deploys to Kubernetes
8. **Verify** → OWASP ZAP performs security testing on live app
9. **Monitor** → Application serves traffic with 2 replicas

## Security Posture

### Shift-Left Security
- Security checks happen early in the pipeline
- Vulnerabilities caught before production
- Automated scanning on every commit

### Defense in Depth
- Multiple security layers (SAST, SCA, DAST)
- Container security with minimal base image
- Kubernetes network policies
- Secret management via GitHub Secrets

### Compliance & Reporting
- SonarCloud dashboard for code quality metrics
- GitHub Security tab for vulnerability tracking
- OWASP ZAP reports for penetration testing results
- Audit trail via Git history

## High Availability

- **Replicas**: 2 pods running simultaneously
- **Load Balancing**: Kubernetes Service distributes traffic
- **Self-Healing**: Kubernetes restarts failed pods automatically
- **Rolling Updates**: Zero-downtime deployments via ArgoCD

## Scalability

The architecture supports horizontal scaling:
- Increase replica count in deployment.yml
- Kubernetes automatically load balances
- Stateless application design enables easy scaling
- Container orchestration handles pod distribution

## Monitoring & Observability

- Spring Boot Actuator endpoints for health checks
- Kubernetes liveness and readiness probes
- ArgoCD deployment status monitoring
- GitHub Actions workflow execution logs
