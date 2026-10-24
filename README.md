# CI/CD Final Project: Automated Build, Test and Deploy Pipeline

**Project Name:** CI/CD Pipeline with GitHub Actions, Tekton and OpenShift

## Overview

This project implements a complete CI/CD pipeline for a sample Python (Flask) application. Every change pushed to the repository is automatically linted, tested, built and deployed to an OpenShift cluster.

## Technologies Used

| Tool | Purpose |
|------|---------|
| GitHub Actions | Continuous Integration: lint and unit tests on every push and pull request |
| flake8 | Python code linting |
| nose | Unit testing and coverage |
| Tekton | Task and pipeline definitions for the OpenShift pipeline |
| OpenShift Pipelines | Runs the Tekton pipeline on the cluster |
| OpenShift | Builds and hosts the deployed application |

## Repository Structure

```
.
├── .github/
│   └── workflows/
│       └── workflow.yml     # GitHub Actions CI workflow
├── .tekton/
│   ├── tasks.yml            # cleanup and nose Tekton tasks
│   ├── pipeline.yml         # Pipeline definition
│   └── pvc.yml              # PersistentVolumeClaim for the shared workspace
├── service/                 # Application source code
├── tests/                   # Unit tests
├── requirements.txt
└── README.md
```

## CI Workflow (GitHub Actions)

Triggered on every push and pull request to `main`:

1. Checkout the code
2. Install dependencies
3. **Lint with flake8**
4. **Run unit tests with nose**

## CD Pipeline (Tekton on OpenShift)

The pipeline runs these tasks in order, sharing a workspace backed by a PersistentVolumeClaim:

1. **cleanup**: clears the workspace
2. **git-clone**: clones the repository
3. **flake8**: lints the code
4. **nose**: runs unit tests
5. **buildah**: builds the container image
6. **deploy**: deploys the application to OpenShift

## How to Run

```bash
# Create the persistent volume claim
oc apply -f .tekton/pvc.yml

# Install tasks and pipeline
oc apply -f .tekton/tasks.yml
oc apply -f .tekton/pipeline.yml

# Start the pipeline and follow the logs
tkn pipeline start cd-pipeline \
  -p repo-url="https://github.com/<your-username>/<your-repo>.git" \
  -p branch="main" \
  -w name=pipeline-workspace,claimName=oc-lab-pvc \
  --showlog
```

## Verifying the Deployment

```bash
oc get pods
oc logs deployment/<app-name>
```

## Author

<your-name>: IBM CI/CD course final project
