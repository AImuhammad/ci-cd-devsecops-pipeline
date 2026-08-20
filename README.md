# CI/CD DevSecOps Pipeline

## Project Overview

This project implements an automated **CI/CD and DevSecOps pipeline** for a Python Flask application.

The pipeline automatically builds, tests, analyzes, security-scans, packages, publishes, and deploys the application whenever changes are pushed to the GitHub repository.

The project demonstrates how security and quality controls can be integrated directly into a CI/CD workflow rather than being performed manually after deployment.

## Architecture

```text
                         ┌─────────────────┐
                         │     GitHub      │
                         │   Source Code   │
                         └────────┬────────┘
                                  │
                              Git Push
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ GitHub Webhook  │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │     Jenkins     │
                         │    CI/CD        │
                         └────────┬────────┘
                                  │
             ┌────────────────────┼────────────────────┐
             │                    │                    │
             ▼                    ▼                    ▼
       ┌───────────┐       ┌────────────┐       ┌───────────┐
       │   Tests   │       │ SonarQube  │       │   Trivy   │
       │   pytest  │       │  Analysis  │       │   Scan    │
       └─────┬─────┘       └─────┬──────┘       └─────┬─────┘
             │                   │                    │
             └───────────────────┼────────────────────┘
                                 │
                                 ▼
                         ┌─────────────────┐
                         │  Docker Build   │
                         │   Image: 5.0    │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   Docker Hub    │
                         │ Image Registry  │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Docker Deploy   │
                         │   Port 5000     │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Health Check    │
                         │ /health         │
                         └─────────────────┘
```

## Objectives

The main objectives of this project are to:

* Automate application testing and deployment.
* Implement continuous integration using Jenkins.
* Integrate source-code quality analysis using SonarQube.
* Perform container vulnerability scanning using Trivy.
* Build reproducible Docker images.
* Publish Docker images to Docker Hub.
* Automatically deploy successful builds.
* Implement automated application health checks.
* Trigger the pipeline automatically using GitHub webhooks.
* Demonstrate DevSecOps principles by integrating security into the CI/CD lifecycle.

## Technology Stack

| Category           | Technology        |
| ------------------ | ----------------- |
| Source Control     | Git / GitHub      |
| CI/CD              | Jenkins           |
| Application        | Python / Flask    |
| Testing            | pytest            |
| Code Quality       | SonarQube         |
| Security Scanning  | Trivy             |
| Containerization   | Docker            |
| Container Registry | Docker Hub        |
| Webhook            | GitHub Webhook    |
| Tunnel             | Cloudflare Tunnel |
| Operating System   | Ubuntu Linux      |

## Application

The application is a lightweight Python Flask service that provides:

* A main application endpoint.
* A health-check endpoint.
* Application version information.

### Application Endpoint

```text
http://localhost:5000
```

### Health Endpoint

```text
http://localhost:5000/health
```

Expected health response:

```json
{
  "status": "healthy"
}
```

Current application release:

```text
Version: 5.0
```
# CI/CD pipeline test
