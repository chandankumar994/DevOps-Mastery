# 🚀 Azure DevOps - Complete Guide for DevOps Engineers

## 📖 Table of Contents

1. [What is Azure DevOps?](#1-what-is-azure-devops)
2. [Core Services Overview](#2-core-services-overview)
3. [Getting Started](#3-getting-started)
4. [Azure Boards](#4-azure-boards)
5. [Azure Repos](#5-azure-repos)
6. [Azure Pipelines](#6-azure-pipelines)
7. [Azure Artifacts](#7-azure-artifacts)
8. [Azure Test Plans](#8-azure-test-plans)
9. [Real-Life Project Walkthrough](#9-real-life-project-walkthrough)
10. [Sample Code & Explanations](#10-sample-code--explanations)
11. [Best Practices](#11-best-practices)
12. [Trending Interview Questions & Answers](#12-trending-interview-questions--answers)
13. [Cheat Sheet](#13-cheat-sheet)

---

## 1. What is Azure DevOps?

Azure DevOps is a **cloud-based platform by Microsoft** that provides a complete set of tools for software development, delivery, and maintenance. Think of it as a **one-stop shop** for everything your development team needs.

### 🏠 Real-Life Analogy

> Imagine you're **building a house**:
> - You need a **blueprint** (Planning → Azure Boards)
> - You need a **warehouse for materials** (Code Storage → Azure Repos)
> - You need **workers to assemble** (Build & Deploy → Azure Pipelines)
> - You need a **supply chain for parts** (Package Management → Azure Artifacts)
> - You need **inspectors to check quality** (Testing → Azure Test Plans)
>
> **Azure DevOps = The entire construction company managing all of this!**

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                      AZURE DEVOPS PLATFORM                       │
│                                                                   │
│  ┌──────────┐  ┌──────────┐  ┌───────────┐  ┌─────────────────┐ │
│  │  AZURE   │  │  AZURE   │  │   AZURE   │  │     AZURE       │ │
│  │  BOARDS  │  │  REPOS   │  │ PIPELINES │  │   ARTIFACTS     │ │
│  │          │  │          │  │           │  │                 │ │
│  │ Planning │  │   Code   │  │ CI/CD     │  │ Package Mgmt    │ │
│  │ Tracking │  │  Storage │  │ Automation│  │ NuGet/npm/Maven │ │
│  └──────────┘  └──────────┘  └───────────┘  └─────────────────┘ │
│                                                                   │
│  ┌──────────────┐  ┌──────────────────────────────────────────┐  │
│  │  AZURE TEST  │  │         EXTENSIONS & MARKETPLACE         │  │
│  │    PLANS     │  │  (1000+ plugins for extra functionality) │  │
│  │   Testing    │  └──────────────────────────────────────────┘  │
│  └──────────────┘                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. Core Services Overview

| Service | Purpose | Real-Life Example |
|---------|---------|-------------------|
| **Azure Boards** | Project planning & tracking | Like a Trello/Jira board for tasks |
| **Azure Repos** | Git repositories for source code | Like GitHub for storing code |
| **Azure Pipelines** | CI/CD automation | Like an assembly line in a factory |
| **Azure Artifacts** | Package management | Like a library shelf for reusable code |
| **Azure Test Plans** | Manual & automated testing | Like quality control inspectors |

### How They Work Together

```
  Developer          Azure Repos        Azure Pipelines       Azure Artifacts      Production
  writes code    →   stores code    →   builds & tests    →   stores packages  →   deploys app
      │                   │                   │                     │                    │
      ▼                   ▼                   ▼                     ▼                    ▼
  ┌────────┐       ┌───────────┐       ┌────────────┐       ┌────────────┐       ┌──────────┐
  │  Code  │──────▶│  Git Repo │──────▶│  Build &   │──────▶│  Package   │──────▶│  Live    │
  │ Change │       │  (Push)   │       │  Test      │       │  Feed      │       │  Server  │
  └────────┘       └───────────┘       └────────────┘       └────────────┘       └──────────┘
      │                                       │
      │                                       ▼
      │                                ┌────────────┐
      └───────── Azure Boards ────────▶│  Track     │
                (Work Items)           │  Progress  │
                                       └────────────┘
```

---

## 3. Getting Started

### Step 1: Create an Azure DevOps Account

1. Go to [dev.azure.com](https://dev.azure.com)
2. Sign in with your Microsoft account (or create one)
3. Click **"Create New Organization"**
4. Name your organization (e.g., `mycompany-devops`)
5. Create your first **Project**

### Step 2: Create a New Project

```
Organization: mycompany-devops
    │
    ├── Project: ecommerce-webapp     ← Your project
    │       ├── Boards
    │       ├── Repos
    │       ├── Pipelines
    │       ├── Test Plans
    │       └── Artifacts
    │
    ├── Project: mobile-app
    └── Project: internal-tools
```

### Step 3: Project Settings

| Setting | Recommended Value | Why |
|---------|------------------|-----|
| Version Control | **Git** | Industry standard |
| Work Item Process | **Agile** | Best for most teams |
| Visibility | **Private** | Security |

---

## 4. Azure Boards

### What is it?

Azure Boards is your **project management tool** — like a digital whiteboard where you track work.

### Work Item Hierarchy

```
┌─────────────────────────────────────────────────┐
│                      EPIC                        │
│         "Build E-Commerce Platform"              │
│                                                   │
│    ┌──────────────────┐  ┌──────────────────┐    │
│    │     FEATURE      │  │     FEATURE      │    │
│    │ "Shopping Cart"  │  │ "User Login"     │    │
│    │                  │  │                  │    │
│    │ ┌──────────────┐ │  │ ┌──────────────┐ │    │
│    │ │  USER STORY  │ │  │ │  USER STORY  │ │    │
│    │ │ "Add items   │ │  │ │ "Login with  │ │    │
│    │ │  to cart"    │ │  │ │  Google"     │ │    │
│    │ │             │ │  │ │             │ │    │
│    │ │ ┌──────────┐│ │  │ │ ┌──────────┐│ │    │
│    │ │ │  TASK    ││ │  │ │ │  TASK    ││ │    │
│    │ │ │"Create   ││ │  │ │ │"Setup    ││ │    │
│    │ │ │ API      ││ │  │ │ │ OAuth"   ││ │    │
│    │ │ │ endpoint"││ │  │ │ └──────────┘│ │    │
│    │ │ └──────────┘│ │  │ └──────────────┘ │    │
│    │ └──────────────┘ │  └──────────────────┘    │
│    └──────────────────┘                          │
└─────────────────────────────────────────────────┘
```

### Kanban Board Example

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│     NEW      │   ACTIVE     │   TESTING    │    DONE      │
├──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │              │
│ ┌──────────┐ │ ┌──────────┐ │ ┌──────────┐ │ ┌──────────┐ │
│ │ Fix bug  │ │ │ Add      │ │ │ Payment  │ │ │ Login    │ │
│ │ #1234    │ │ │ search   │ │ │ gateway  │ │ │ feature  │ │
│ └──────────┘ │ │ filter   │ │ └──────────┘ │ └──────────┘ │
│              │ └──────────┘ │              │              │
│ ┌──────────┐ │              │              │ ┌──────────┐ │
│ │ Update   │ │ ┌──────────┐ │              │ │ Database │ │
│ │ docs     │ │ │ User     │ │              │ │ setup    │ │
│ └──────────┘ │ │ profile  │ │              │ └──────────┘ │
│              │ └──────────┘ │              │              │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

### Sprint Planning

```
Sprint 1 (2 weeks)           Sprint 2 (2 weeks)
├── User Story: Login        ├── User Story: Dashboard
│   ├── Task: UI Design      │   ├── Task: Charts API
│   ├── Task: API Setup      │   ├── Task: UI Layout
│   └── Task: Testing        │   └── Task: Testing
├── User Story: Registration └── User Story: Reports
│   ├── Task: Form Design        ├── Task: PDF Export
│   └── Task: Validation         └── Task: Email Reports
└── Bug: Fix header #102
```

---

## 5. Azure Repos

### What is it?

Azure Repos is **Git-based source code management** — it stores your code and tracks every change.

### Git Branching Strategy

```
main (production)
  │
  ├──── develop (integration branch)
  │       │
  │       ├──── feature/login ──────── merged back ──┐
  │       │                                           │
  │       ├──── feature/cart ───────── merged back ──┤
  │       │                                           │
  │       ◄───────────────────────────────────────────┘
  │       │
  │       ├──── release/v1.0 ──────── merged to main ──┐
  │       │                                              │
  │       │                                              │
  ◄──────────────────────────────────────────────────────┘
  │
  ├──── hotfix/critical-bug ────── merged to main & develop
```

### Setting Up a Repo

```bash
# Clone the repo from Azure DevOps
git clone https://dev.azure.com/mycompany/myproject/_git/myrepo

# Navigate into the project
cd myrepo

# Create a new branch for your feature
git checkout -b feature/add-shopping-cart

# Make your changes, then stage and commit
git add .
git commit -m "Added shopping cart functionality"

# Push the branch to Azure Repos
git push origin feature/add-shopping-cart
```

### Pull Request (PR) Workflow

```
Developer A                    Azure Repos                 Developer B (Reviewer)
    │                              │                              │
    │  1. Push feature branch      │                              │
    │─────────────────────────────▶│                              │
    │                              │                              │
    │  2. Create Pull Request      │  3. PR Notification          │
    │─────────────────────────────▶│─────────────────────────────▶│
    │                              │                              │
    │                              │  4. Code Review              │
    │                              │◀─────────────────────────────│
    │                              │                              │
    │                              │  5. Comments/Suggestions     │
    │◀─────────────────────────────│◀─────────────────────────────│
    │                              │                              │
    │  6. Fix review comments      │                              │
    │─────────────────────────────▶│                              │
    │                              │                              │
    │                              │  7. Approve ✅               │
    │                              │◀─────────────────────────────│
    │                              │                              │
    │  8. Merge to main            │                              │
    │◀─────────────────────────────│                              │
```

### Branch Policies (Recommended)

```yaml
# Settings for 'main' branch
Branch Policies:
  ✅ Require minimum 2 reviewers
  ✅ Check for linked work items
  ✅ Check for comment resolution
  ✅ Build validation (CI must pass)
  ✅ Require merge strategy: Squash merge
```

---

## 6. Azure Pipelines

### What is it?

Azure Pipelines is the **heart of CI/CD** — it automatically builds, tests, and deploys your application.

### 🏭 Real-Life Analogy

> Think of Azure Pipelines like a **car manufacturing assembly line**:
> 1. **Raw materials come in** (source code from repo)
> 2. **Parts are assembled** (code is compiled/built)
> 3. **Quality inspection** (automated tests run)
> 4. **Car is shipped to dealer** (deployed to server)
>
> If any step fails, the line stops! 🛑

### CI/CD Pipeline Flow

```
┌──────────────────────────────────────────────────────────────────────────┐
│                        CONTINUOUS INTEGRATION (CI)                        │
│                                                                           │
│   ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────────────────┐  │
│   │  Code   │───▶│  Build  │───▶│  Test   │───▶│  Create Artifact    │  │
│   │  Push   │    │         │    │         │    │  (Package/Image)    │  │
│   └─────────┘    └─────────┘    └─────────┘    └─────────────────────┘  │
│                                                           │              │
└───────────────────────────────────────────────────────────│──────────────┘
                                                            │
┌───────────────────────────────────────────────────────────│──────────────┐
│                    CONTINUOUS DEPLOYMENT (CD)              │              │
│                                                           ▼              │
│   ┌─────────────┐    ┌─────────────┐    ┌─────────────────────────────┐ │
│   │    DEV      │───▶│   STAGING   │───▶│       PRODUCTION           │ │
│   │ Environment │    │ Environment │    │      Environment           │ │
│   │ (Auto)      │    │ (Auto)      │    │ (Manual Approval Gate)     │ │
│   └─────────────┘    └─────────────┘    └─────────────────────────────┘ │
│                                                                          │
└──────────────────────────────────────────────────────────────────────────┘
```

### YAML Pipeline - Basic Structure

```yaml
# azure-pipelines.yml - This file lives in your repo root

# ──────────────────────────────────────────
# TRIGGER: When should the pipeline run?
# ──────────────────────────────────────────
trigger:
  branches:
    include:
      - main        # Run when code is pushed to 'main'
      - develop     # Run when code is pushed to 'develop'
  paths:
    exclude:
      - '**/*.md'   # Don't run for markdown file changes

# ──────────────────────────────────────────
# POOL: Where should the pipeline run?
# ──────────────────────────────────────────
pool:
  vmImage: 'ubuntu-latest'    # Use Microsoft-hosted Ubuntu agent
  # Other options: 'windows-latest', 'macOS-latest'

# ──────────────────────────────────────────
# VARIABLES: Reusable values
# ──────────────────────────────────────────
variables:
  buildConfiguration: 'Release'
  dotnetVersion: '8.0.x'

# ──────────────────────────────────────────
# STAGES: High-level phases
# ──────────────────────────────────────────
stages:
  - stage: Build
    displayName: '🔨 Build Stage'
    jobs:
      - job: BuildJob
        displayName: 'Build the application'
        steps:
          - task: UseDotNet@2
            displayName: 'Install .NET SDK'
            inputs:
              version: '$(dotnetVersion)'

          - script: dotnet restore
            displayName: '📦 Restore NuGet Packages'

          - script: dotnet build --configuration $(buildConfiguration)
            displayName: '🔨 Build Project'

          - script: dotnet test --configuration $(buildConfiguration)
            displayName: '🧪 Run Unit Tests'

          - task: PublishBuildArtifacts@1
            displayName: '📤 Publish Artifact'
            inputs:
              pathtoPublish: '$(Build.ArtifactStagingDirectory)'
              artifactName: 'drop'

  - stage: DeployDev
    displayName: '🚀 Deploy to DEV'
    dependsOn: Build
    jobs:
      - deployment: DeployDev
        environment: 'dev'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying to DEV environment..."
                  displayName: 'Deploy to Dev'

  - stage: DeployProd
    displayName: '🏭 Deploy to PRODUCTION'
    dependsOn: DeployDev
    jobs:
      - deployment: DeployProd
        environment: 'production'       # Has approval gate configured
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying to PRODUCTION..."
                  displayName: 'Deploy to Production'
```

### Pipeline Concepts Explained

```
┌──────────────────────────────────────────────────────────┐
│                       PIPELINE                            │
│                                                           │
│  ┌─────────────────────────────────────────────────────┐ │
│  │                    STAGE 1: Build                    │ │
│  │                                                      │ │
│  │  ┌───────────────────────────────────────────────┐  │ │
│  │  │              JOB 1: Compile                    │  │ │
│  │  │                                                │  │ │
│  │  │  ┌────────┐  ┌────────┐  ┌────────┐          │  │ │
│  │  │  │ Step 1 │─▶│ Step 2 │─▶│ Step 3 │          │  │ │
│  │  │  │Restore │  │ Build  │  │ Test   │          │  │ │
│  │  │  └────────┘  └────────┘  └────────┘          │  │ │
│  │  └───────────────────────────────────────────────┘  │ │
│  └─────────────────────────────────────────────────────┘ │
│                          │                                │
│                          ▼                                │
│  ┌─────────────────────────────────────────────────────┐ │
│  │              STAGE 2: Deploy to Dev                  │ │
│  │                                                      │ │
│  │  ┌───────────────────────────────────────────────┐  │ │
│  │  │          JOB 1: Deploy                         │  │ │
│  │  │                                                │  │ │
│  │  │  ┌────────────┐  ┌──────────────┐             │  │ │
│  │  │  │ Download   │─▶│ Deploy to    │             │  │ │
│  │  │  │ Artifact   │  │ App Service  │             │  │ │
│  │  │  └────────────┘  └──────────────┘             │  │ │
│  │  └───────────────────────────────────────────────┘  │ │
│  └─────────────────────────────────────────────────────┘ │
│                          │                                │
│                   🔒 APPROVAL GATE                       │
│                          │                                │
│                          ▼                                │
│  ┌─────────────────────────────────────────────────────┐ │
│  │            STAGE 3: Deploy to Production             │ │
│  └─────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────┘
```

### Common Pipeline Templates

#### Node.js Application

```yaml
# Node.js CI/CD Pipeline
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  nodeVersion: '18.x'

stages:
  - stage: Build
    jobs:
      - job: Build
        steps:
          # Install Node.js
          - task: NodeTool@0
            inputs:
              versionSpec: '$(nodeVersion)'
            displayName: '📦 Install Node.js'

          # Install dependencies
          - script: npm ci
            displayName: '📦 Install Dependencies'

          # Run linting
          - script: npm run lint
            displayName: '🔍 Run Linter'

          # Run tests with coverage
          - script: npm run test -- --coverage
            displayName: '🧪 Run Tests'

          # Publish test results
          - task: PublishTestResults@2
            inputs:
              testResultsFormat: 'JUnit'
              testResultsFiles: '**/junit.xml'
            displayName: '📊 Publish Test Results'

          # Build the application
          - script: npm run build
            displayName: '🔨 Build Application'

          # Copy files to staging
          - task: CopyFiles@2
            inputs:
              sourceFolder: '$(Build.SourcesDirectory)/dist'
              contents: '**'
              targetFolder: '$(Build.ArtifactStagingDirectory)'

          # Publish artifact
          - task: PublishBuildArtifacts@1
            inputs:
              pathtoPublish: '$(Build.ArtifactStagingDirectory)'
              artifactName: 'webapp'

  - stage: Deploy
    dependsOn: Build
    jobs:
      - deployment: DeployToAzure
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                  inputs:
                    azureSubscription: 'MyAzureServiceConnection'
                    appName: 'my-nodejs-app'
                    package: '$(Pipeline.Workspace)/webapp/**'
```

#### Docker Build & Push Pipeline

```yaml
# Docker CI/CD Pipeline
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  dockerRegistryServiceConnection: 'myACRConnection'
  imageRepository: 'myapp'
  containerRegistry: 'myregistry.azurecr.io'
  dockerfilePath: '$(Build.SourcesDirectory)/Dockerfile'
  tag: '$(Build.BuildId)'

stages:
  - stage: Build
    displayName: '🐳 Build Docker Image'
    jobs:
      - job: Build
        steps:
          # Build and push Docker image
          - task: Docker@2
            displayName: 'Build and Push Image'
            inputs:
              command: buildAndPush
              repository: $(imageRepository)
              dockerfile: $(dockerfilePath)
              containerRegistry: $(dockerRegistryServiceConnection)
              tags: |
                $(tag)
                latest

  - stage: Deploy
    displayName: '🚀 Deploy to AKS'
    dependsOn: Build
    jobs:
      - deployment: Deploy
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: KubernetesManifest@0
                  displayName: 'Deploy to Kubernetes'
                  inputs:
                    action: deploy
                    kubernetesServiceConnection: 'myAKSConnection'
                    namespace: 'default'
                    manifests: |
                      $(Pipeline.Workspace)/manifests/deployment.yml
                      $(Pipeline.Workspace)/manifests/service.yml
                    containers: |
                      $(containerRegistry)/$(imageRepository):$(tag)
```

#### Terraform Infrastructure Pipeline

```yaml
# Terraform CI/CD Pipeline
trigger:
  branches:
    include:
      - main
  paths:
    include:
      - 'infrastructure/**'

pool:
  vmImage: 'ubuntu-latest'

variables:
  terraformVersion: '1.6.0'
  workingDirectory: '$(System.DefaultWorkingDirectory)/infrastructure'

stages:
  - stage: Validate
    displayName: '✅ Validate Terraform'
    jobs:
      - job: Validate
        steps:
          - task: TerraformInstaller@0
            inputs:
              terraformVersion: '$(terraformVersion)'

          - task: TerraformTaskV4@4
            displayName: 'Terraform Init'
            inputs:
              provider: 'azurerm'
              command: 'init'
              workingDirectory: '$(workingDirectory)'
              backendServiceArm: 'AzureServiceConnection'
              backendAzureRmResourceGroupName: 'tf-state-rg'
              backendAzureRmStorageAccountName: 'tfstatestorage'
              backendAzureRmContainerName: 'tfstate'
              backendAzureRmKey: 'terraform.tfstate'

          - task: TerraformTaskV4@4
            displayName: 'Terraform Validate'
            inputs:
              provider: 'azurerm'
              command: 'validate'
              workingDirectory: '$(workingDirectory)'

  - stage: Plan
    displayName: '📋 Terraform Plan'
    dependsOn: Validate
    jobs:
      - job: Plan
        steps:
          - task: TerraformInstaller@0
            inputs:
              terraformVersion: '$(terraformVersion)'

          - task: TerraformTaskV4@4
            displayName: 'Terraform Init'
            inputs:
              provider: 'azurerm'
              command: 'init'
              workingDirectory: '$(workingDirectory)'
              backendServiceArm: 'AzureServiceConnection'
              backendAzureRmResourceGroupName: 'tf-state-rg'
              backendAzureRmStorageAccountName: 'tfstatestorage'
              backendAzureRmContainerName: 'tfstate'
              backendAzureRmKey: 'terraform.tfstate'

          - task: TerraformTaskV4@4
            displayName: 'Terraform Plan'
            inputs:
              provider: 'azurerm'
              command: 'plan'
              workingDirectory: '$(workingDirectory)'
              environmentServiceNameAzureRM: 'AzureServiceConnection'

  - stage: Apply
    displayName: '🚀 Terraform Apply'
    dependsOn: Plan
    jobs:
      - deployment: Apply
        environment: 'infrastructure-prod'    # Requires approval
        strategy:
          runOnce:
            deploy:
              steps:
                - task: TerraformInstaller@0
                  inputs:
                    terraformVersion: '$(terraformVersion)'

                - task: TerraformTaskV4@4
                  displayName: 'Terraform Init'
                  inputs:
                    provider: 'azurerm'
                    command: 'init'
                    workingDirectory: '$(workingDirectory)'
                    backendServiceArm: 'AzureServiceConnection'
                    backendAzureRmResourceGroupName: 'tf-state-rg'
                    backendAzureRmStorageAccountName: 'tfstatestorage'
                    backendAzureRmContainerName: 'tfstate'
                    backendAzureRmKey: 'terraform.tfstate'

                - task: TerraformTaskV4@4
                  displayName: 'Terraform Apply'
                  inputs:
                    provider: 'azurerm'
                    command: 'apply'
                    workingDirectory: '$(workingDirectory)'
                    environmentServiceNameAzureRM: 'AzureServiceConnection'
```

### Agent Types

```
┌─────────────────────────────────────────────────────────┐
│                    PIPELINE AGENTS                        │
│                                                           │
│  ┌──────────────────────┐  ┌──────────────────────────┐  │
│  │  Microsoft-Hosted    │  │    Self-Hosted Agent     │  │
│  │     Agent            │  │                          │  │
│  │                      │  │  ┌────────────────────┐  │  │
│  │  ✅ No maintenance   │  │  │  Your own VM/      │  │  │
│  │  ✅ Pre-installed    │  │  │  Container/Server  │  │  │
│  │     tools            │  │  └────────────────────┘  │  │
│  │  ✅ Auto-scales      │  │                          │  │
│  │  ❌ Limited build    │  │  ✅ Full control         │  │
│  │     time (60 min)    │  │  ✅ Cached dependencies  │  │
│  │  ❌ Fresh VM each    │  │  ✅ Access to private    │  │
│  │     time             │  │     networks            │  │
│  │                      │  │  ❌ You manage it       │  │
│  │  Images:             │  │                          │  │
│  │  • ubuntu-latest     │  │  Setup:                  │  │
│  │  • windows-latest    │  │  1. Download agent       │  │
│  │  • macOS-latest      │  │  2. Configure PAT token  │  │
│  │                      │  │  3. Register with pool   │  │
│  └──────────────────────┘  └──────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### Setting Up a Self-Hosted Agent

```bash
# Step 1: Download the agent
mkdir myagent && cd myagent
wget https://vstsagentpackage.azureedge.net/agent/3.227.2/vsts-agent-linux-x64-3.227.2.tar.gz
tar zxvf vsts-agent-linux-x64-3.227.2.tar.gz

# Step 2: Configure the agent
./config.sh
# Enter server URL: https://dev.azure.com/mycompany
# Enter PAT token: xxxxxxxxxx
# Enter agent pool: Default
# Enter agent name: my-build-agent

# Step 3: Run the agent
./run.sh

# Step 4: (Optional) Install as a service
sudo ./svc.sh install
sudo ./svc.sh start
```

---

## 7. Azure Artifacts

### What is it?

Azure Artifacts is a **package management service** — it stores reusable code packages that your projects can depend on.

### Supported Package Types

```
┌────────────────────────────────────────────────┐
│              AZURE ARTIFACTS FEEDS              │
│                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐     │
│  │  NuGet   │  │   npm    │  │  Maven   │     │
│  │  (.NET)  │  │ (Node.js)│  │  (Java)  │     │
│  └──────────┘  └──────────┘  └──────────┘     │
│                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐     │
│  │  PyPI    │  │  Cargo   │  │Universal │     │
│  │ (Python) │  │  (Rust)  │  │ Packages │     │
│  └──────────┘  └──────────┘  └──────────┘     │
└────────────────────────────────────────────────┘
```

### Setting Up npm Feed

```bash
# Step 1: Create a .npmrc file in your project root
registry=https://pkgs.dev.azure.com/mycompany/_packaging/my-feed/npm/registry/
always-auth=true

# Step 2: Authenticate
npx vsts-npm-auth -config .npmrc

# Step 3: Publish your package
npm publish

# Step 4: Install from your feed
npm install @mycompany/shared-utils
```

### Using Artifacts in Pipeline

```yaml
steps:
  # Restore from Azure Artifacts feed
  - task: NuGetCommand@2
    inputs:
      command: 'restore'
      restoreSolution: '**/*.sln'
      feedsToUse: 'select'
      vstsFeed: 'my-feed'    # Your Azure Artifacts feed name

  # Push package to Azure Artifacts
  - task: NuGetCommand@2
    inputs:
      command: 'push'
      packagesToPush: '$(Build.ArtifactStagingDirectory)/**/*.nupkg'
      nuGetFeedType: 'internal'
      publishVstsFeed: 'my-feed'
```

---

## 8. Azure Test Plans

### What is it?

Azure Test Plans helps you manage **manual and exploratory testing** as well as **automated test tracking**.

### Test Plan Structure

```
Test Plan: "Release 2.0 Testing"
│
├── Test Suite: "Login Module"
│   ├── Test Case: "Valid credentials login"
│   │   ├── Step 1: Navigate to login page
│   │   ├── Step 2: Enter valid username
│   │   ├── Step 3: Enter valid password
│   │   ├── Step 4: Click login button
│   │   └── Expected: User is redirected to dashboard
│   │
│   ├── Test Case: "Invalid password attempt"
│   │   ├── Step 1: Navigate to login page
│   │   ├── Step 2: Enter valid username
│   │   ├── Step 3: Enter wrong password
│   │   ├── Step 4: Click login button
│   │   └── Expected: Error message displayed
│   │
│   └── Test Case: "Account lockout after 5 attempts"
│
├── Test Suite: "Shopping Cart"
│   ├── Test Case: "Add item to cart"
│   ├── Test Case: "Remove item from cart"
│   └── Test Case: "Update quantity"
│
└── Test Suite: "Checkout Process"
    ├── Test Case: "Complete purchase with credit card"
    └── Test Case: "Apply discount code"
```

### Running Automated Tests in Pipeline

```yaml
# Run tests and publish results
steps:
  - script: dotnet test --logger trx --results-directory $(Agent.TempDirectory)
    displayName: 'Run Automated Tests'

  - task: PublishTestResults@2
    inputs:
      testRunner: 'VSTest'
      testResultsFiles: '$(Agent.TempDirectory)/**/*.trx'
      mergeTestResults: true
      testRunTitle: 'Unit Tests - Build $(Build.BuildId)'
    displayName: 'Publish Test Results'

  # Publish code coverage
  - task: PublishCodeCoverageResults@1
    inputs:
      codeCoverageTool: 'Cobertura'
      summaryFileLocation: '$(Agent.TempDirectory)/**/coverage.cobertura.xml'
    displayName: 'Publish Code Coverage'
```

---

## 9. Real-Life Project Walkthrough

### 🛒 Scenario: Building an E-Commerce Web Application

Let's walk through a complete real-world project using all Azure DevOps services.

```
┌─────────────────────────────────────────────────────────────────┐
│                  E-COMMERCE PROJECT ARCHITECTURE                 │
│                                                                   │
│  ┌────────────┐     ┌────────────┐     ┌────────────────────┐   │
│  │  React     │────▶│  Node.js   │────▶│   Azure SQL        │   │
│  │  Frontend  │     │  Backend   │     │   Database         │   │
│  │  (SPA)     │     │  (API)     │     │                    │   │
│  └────────────┘     └────────────┘     └────────────────────┘   │
│       │                   │                                      │
│       ▼                   ▼                                      │
│  ┌────────────┐     ┌────────────┐                              │
│  │  Azure     │     │  Azure     │                              │
│  │  Static    │     │  App       │                              │
│  │  Web App   │     │  Service   │                              │
│  └────────────┘     └────────────┘                              │
└─────────────────────────────────────────────────────────────────┘
```

### Step-by-Step Implementation

#### Step 1: Set Up Azure Boards

```
Epic: E-Commerce Platform v1.0
│
├── Feature: User Authentication
│   ├── User Story: As a user, I want to register with email
│   │   ├── Task: Create registration API endpoint
│   │   ├── Task: Design registration form (React)
│   │   ├── Task: Add email validation
│   │   └── Task: Write unit tests
│   └── User Story: As a user, I want to login securely
│
├── Feature: Product Catalog
│   ├── User Story: As a user, I want to browse products
│   └── User Story: As a user, I want to search products
│
├── Feature: Shopping Cart
│   ├── User Story: As a user, I want to add items to cart
│   └── User Story: As a user, I want to checkout
│
└── Feature: Admin Dashboard
    └── User Story: As an admin, I want to manage inventory
```

#### Step 2: Set Up Repo Structure

```
ecommerce-app/
├── .azure-pipelines/
│   ├── ci-pipeline.yml
│   ├── cd-pipeline.yml
│   └── templates/
│       ├── build-template.yml
│       └── deploy-template.yml
├── src/
│   ├── frontend/           # React app
│   │   ├── src/
│   │   ├── package.json
│   │   └── Dockerfile
│   ├── backend/            # Node.js API
│   │   ├── src/
│   │   ├── package.json
│   │   └── Dockerfile
│   └── shared/             # Shared utilities
│       └── package.json
├── infrastructure/          # Terraform IaC
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
└── README.md
```

#### Step 3: Create the CI/CD Pipeline

```yaml
# .azure-pipelines/ci-pipeline.yml
# Complete CI/CD for E-Commerce App

trigger:
  branches:
    include:
      - main
      - develop
      - feature/*

pr:
  branches:
    include:
      - main
      - develop

pool:
  vmImage: 'ubuntu-latest'

variables:
  - group: 'ecommerce-variables'    # Variable group from Library
  - name: nodeVersion
    value: '18.x'
  - name: dockerRegistry
    value: 'ecommerceacr.azurecr.io'

# ═══════════════════════════════════════
# STAGE 1: BUILD & TEST
# ═══════════════════════════════════════
stages:
  - stage: BuildAndTest
    displayName: '🔨 Build & Test'
    jobs:
      # ─── Frontend Build ───
      - job: Frontend
        displayName: 'Build React Frontend'
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '$(nodeVersion)'

          - script: |
              cd src/frontend
              npm ci
              npm run lint
              npm run test -- --coverage --watchAll=false
              npm run build
            displayName: '🎨 Build Frontend'

          - task: PublishTestResults@2
            inputs:
              testResultsFormat: 'JUnit'
              testResultsFiles: 'src/frontend/coverage/junit.xml'
              testRunTitle: 'Frontend Tests'

          - task: PublishCodeCoverageResults@1
            inputs:
              codeCoverageTool: 'Cobertura'
              summaryFileLocation: 'src/frontend/coverage/cobertura-coverage.xml'

          - task: PublishBuildArtifacts@1
            inputs:
              pathtoPublish: 'src/frontend/build'
              artifactName: 'frontend'

      # ─── Backend Build ───
      - job: Backend
        displayName: 'Build Node.js Backend'
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '$(nodeVersion)'

          - script: |
              cd src/backend
              npm ci
              npm run lint
              npm test
              npm run build
            displayName: '⚙️ Build Backend'

          - task: PublishBuildArtifacts@1
            inputs:
              pathtoPublish: 'src/backend/dist'
              artifactName: 'backend'

      # ─── Docker Build ───
      - job: DockerBuild
        displayName: 'Build Docker Images'
        dependsOn:
          - Frontend
          - Backend
        steps:
          - task: Docker@2
            displayName: '🐳 Build Frontend Image'
            inputs:
              command: buildAndPush
              repository: 'ecommerce-frontend'
              dockerfile: 'src/frontend/Dockerfile'
              containerRegistry: '$(dockerRegistry)'
              tags: |
                $(Build.BuildId)
                latest

          - task: Docker@2
            displayName: '🐳 Build Backend Image'
            inputs:
              command: buildAndPush
              repository: 'ecommerce-backend'
              dockerfile: 'src/backend/Dockerfile'
              containerRegistry: '$(dockerRegistry)'
              tags: |
                $(Build.BuildId)
                latest

  # ═══════════════════════════════════════
  # STAGE 2: DEPLOY TO DEV
  # ═══════════════════════════════════════
  - stage: DeployDev
    displayName: '🚀 Deploy to DEV'
    dependsOn: BuildAndTest
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/develop'))
    jobs:
      - deployment: DeployDev
        environment: 'dev'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                  displayName: 'Deploy Frontend to Dev'
                  inputs:
                    azureSubscription: 'AzureServiceConnection'
                    appName: 'ecommerce-frontend-dev'
                    package: '$(Pipeline.Workspace)/frontend/**'

                - task: AzureWebApp@1
                  displayName: 'Deploy Backend to Dev'
                  inputs:
                    azureSubscription: 'AzureServiceConnection'
                    appName: 'ecommerce-backend-dev'
                    package: '$(Pipeline.Workspace)/backend/**'

  # ═══════════════════════════════════════
  # STAGE 3: DEPLOY TO STAGING
  # ═══════════════════════════════════════
  - stage: DeployStaging
    displayName: '🎭 Deploy to STAGING'
    dependsOn: DeployDev
    jobs:
      - deployment: DeployStaging
        environment: 'staging'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying to staging..."
                  displayName: 'Deploy to Staging'

                # Run integration tests against staging
                - script: |
                    cd tests/integration
                    npm test
                  displayName: '🧪 Run Integration Tests'

  # ═══════════════════════════════════════
  # STAGE 4: DEPLOY TO PRODUCTION
  # ═══════════════════════════════════════
  - stage: DeployProd
    displayName: '🏭 Deploy to PRODUCTION'
    dependsOn: DeployStaging
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
    jobs:
      - deployment: DeployProd
        environment: 'production'    # ⚠️ Requires manual approval!
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                  displayName: 'Deploy to Production'
                  inputs:
                    azureSubscription: 'AzureServiceConnection'
                    appName: 'ecommerce-frontend-prod'
                    deployToSlotOrASE: true
                    resourceGroupName: 'production-rg'
                    slotName: 'staging'     # Deploy to staging slot first

                # Swap slots for zero-downtime deployment
                - task: AzureAppServiceManage@0
                  displayName: 'Swap Staging ↔ Production Slot'
                  inputs:
                    azureSubscription: 'AzureServiceConnection'
                    action: 'Swap Slots'
                    webAppName: 'ecommerce-frontend-prod'
                    resourceGroupName: 'production-rg'
                    sourceSlot: 'staging'
```

### Complete Pipeline Visualization

```
Code Push to 'develop'
        │
        ▼
┌───────────────────┐
│   Build & Test    │
│                   │
│  ┌─────────────┐  │
│  │  Frontend   │  │
│  │  Build+Test │  │
│  └──────┬──────┘  │
│         │         │     ┌─────────────┐
│  ┌──────▼──────┐  │────▶│  Docker     │
│  │  Backend    │  │     │  Build &    │
│  │  Build+Test │  │     │  Push       │
│  └─────────────┘  │     └──────┬──────┘
└───────────────────┘            │
                                 ▼
                    ┌─────────────────────┐
                    │   Deploy to DEV     │
                    │   (Automatic)       │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Deploy to STAGING  │
                    │  + Integration      │
                    │    Tests            │
                    └──────────┬──────────┘
                               │
                          🔒 PR to main
                          🔒 Manual Approval
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Deploy to PROD     │
                    │  (Blue-Green via    │
                    │   Slot Swap)        │
                    └─────────────────────┘
```

---

## 10. Sample Code & Explanations

### Example 1: Multi-Stage Pipeline with Templates

```yaml
# templates/build-template.yml
parameters:
  - name: projectPath
    type: string
  - name: buildConfiguration
    type: string
    default: 'Release'

steps:
  - task: DotNetCoreCLI@2
    displayName: 'Restore packages'
    inputs:
      command: 'restore'
      projects: '${{ parameters.projectPath }}/**/*.csproj'

  - task: DotNetCoreCLI@2
    displayName: 'Build project'
    inputs:
      command: 'build'
      projects: '${{ parameters.projectPath }}/**/*.csproj'
      arguments: '--configuration ${{ parameters.buildConfiguration }}'

  - task: DotNetCoreCLI@2
    displayName: 'Run tests'
    inputs:
      command: 'test'
      projects: '${{ parameters.projectPath }}/**/*Tests.csproj'
      arguments: '--configuration ${{ parameters.buildConfiguration }} --collect "Code Coverage"'
```

```yaml
# azure-pipelines.yml (main pipeline using template)
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

stages:
  - stage: BuildAPI
    displayName: 'Build API Service'
    jobs:
      - job: Build
        steps:
          - template: templates/build-template.yml
            parameters:
              projectPath: 'src/api'
              buildConfiguration: 'Release'

  - stage: BuildWorker
    displayName: 'Build Worker Service'
    jobs:
      - job: Build
        steps:
          - template: templates/build-template.yml
            parameters:
              projectPath: 'src/worker'
              buildConfiguration: 'Release'
```

### Example 2: Variable Groups & Key Vault Integration

```yaml
# Using Variable Groups and Azure Key Vault secrets
variables:
  - group: 'production-variables'    # From Library
  - name: environment
    value: 'production'

steps:
  - task: AzureKeyVault@2
    displayName: '🔑 Fetch secrets from Key Vault'
    inputs:
      azureSubscription: 'AzureServiceConnection'
      KeyVaultName: 'my-keyvault'
      SecretsFilter: 'db-connection-string, api-key, jwt-secret'
      RunAsPreJob: true

  # Now use the secrets as variables
  - script: |
      echo "Setting up configuration..."
      # Secrets are automatically mapped to variables
      # $(db-connection-string) → available but masked in logs
    displayName: 'Configure Application'
    env:
      DB_CONNECTION: $(db-connection-string)
      API_KEY: $(api-key)
      JWT_SECRET: $(jwt-secret)
```

### Example 3: Conditional Logic in Pipelines

```yaml
stages:
  - stage: Build
    jobs:
      - job: Build
        steps:
          - script: echo "Building..."

  # Only deploy to dev from develop branch
  - stage: DeployDev
    condition: eq(variables['Build.SourceBranch'], 'refs/heads/develop')
    dependsOn: Build
    jobs:
      - job: Deploy
        steps:
          - script: echo "Deploying to Dev..."

  # Only deploy to prod from main branch AND if it's not a PR
  - stage: DeployProd
    condition: |
      and(
        succeeded(),
        eq(variables['Build.SourceBranch'], 'refs/heads/main'),
        ne(variables['Build.Reason'], 'PullRequest')
      )
    dependsOn: Build
    jobs:
      - deployment: Deploy
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying to Production..."
```

### Example 4: Matrix Strategy (Multiple Configurations)

```yaml
# Build and test on multiple OS and Node.js versions
jobs:
  - job: Test
    strategy:
      matrix:
        linux_node16:
          vmImage: 'ubuntu-latest'
          nodeVersion: '16.x'
        linux_node18:
          vmImage: 'ubuntu-latest'
          nodeVersion: '18.x'
        windows_node18:
          vmImage: 'windows-latest'
          nodeVersion: '18.x'
        mac_node18:
          vmImage: 'macOS-latest'
          nodeVersion: '18.x'
      maxParallel: 4

    pool:
      vmImage: $(vmImage)

    steps:
      - task: NodeTool@0
        inputs:
          versionSpec: '$(nodeVersion)'
      - script: |
          npm ci
          npm test
        displayName: 'Install and Test'
```

### Example 5: Service Connections & Environments

```
┌─────────────────────────────────────────────────────────┐
│              SERVICE CONNECTIONS                          │
│                                                           │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Azure Resource Manager (ARM)                      │  │
│  │  → Connect to Azure Subscription                   │  │
│  │  → Deploy to App Services, AKS, VMs               │  │
│  └────────────────────────────────────────────────────┘  │
│                                                           │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Docker Registry                                   │  │
│  │  → Connect to ACR, Docker Hub                      │  │
│  │  → Push/Pull container images                      │  │
│  └────────────────────────────────────────────────────┘  │
│                                                           │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Kubernetes                                        │  │
│  │  → Connect to AKS or any K8s cluster              │  │
│  │  → Deploy manifests                                │  │
│  └────────────────────────────────────────────────────┘  │
│                                                           │
│  ┌────────────────────────────────────────────────────┐  │
│  │  GitHub / External Git                             │  │
│  │  → Connect to external repositories               │  │
│  └────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### Example 6: Complete Dockerfile for Pipeline

```dockerfile
# Dockerfile for Node.js Backend
# ── Stage 1: Build ──
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

# ── Stage 2: Production ──
FROM node:18-alpine AS production
WORKDIR /app

# Create non-root user for security
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodeuser -u 1001

COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

USER nodeuser
EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000/health || exit 1

CMD ["node", "dist/server.js"]
```

---

## 11. Best Practices

### Pipeline Best Practices

```
┌───────────────────────────────────────────────────────────────┐
│                    BEST PRACTICES CHECKLIST                     │
│                                                                 │
│  CI/CD Pipeline:                                                │
│  ✅ Use YAML pipelines (version-controlled, not Classic UI)    │
│  ✅ Use templates for reusable steps                           │
│  ✅ Use variable groups for environment-specific configs       │
│  ✅ Store secrets in Azure Key Vault (not pipeline variables)  │
│  ✅ Use approval gates for production deployments              │
│  ✅ Implement blue-green or canary deployments                 │
│  ✅ Cache dependencies (npm, NuGet) for faster builds          │
│  ✅ Run tests in parallel when possible                        │
│  ✅ Use matrix strategy for cross-platform testing             │
│  ✅ Set meaningful display names for all steps                 │
│                                                                 │
│  Azure Repos:                                                   │
│  ✅ Enforce branch policies on main/develop                    │
│  ✅ Require PR reviews (minimum 2 reviewers)                   │
│  ✅ Enable build validation on PRs                             │
│  ✅ Use squash merge for clean history                         │
│  ✅ Delete source branches after merge                         │
│                                                                 │
│  Security:                                                      │
│  ✅ Use Service Principals (not personal credentials)          │
│  ✅ Apply least-privilege access                               │
│  ✅ Rotate secrets regularly                                   │
│  ✅ Scan code for vulnerabilities (SonarQube, WhiteSource)     │
│  ✅ Sign container images                                      │
│                                                                 │
│  Monitoring:                                                    │
│  ✅ Set up pipeline notifications                              │
│  ✅ Monitor build times and success rates                      │
│  ✅ Use dashboards for team visibility                         │
│  ✅ Set up alerts for failed deployments                       │
└───────────────────────────────────────────────────────────────┘
```

### Caching Dependencies (Speed Up Builds)

```yaml
# Cache npm packages to speed up builds
steps:
  - task: Cache@2
    displayName: '📦 Cache npm packages'
    inputs:
      key: 'npm | "$(Agent.OS)" | src/frontend/package-lock.json'
      path: 'src/frontend/node_modules'
      cacheHitVar: 'CACHE_RESTORED'

  - script: |
      cd src/frontend
      npm ci
    displayName: 'Install Dependencies'
    condition: ne(variables.CACHE_RESTORED, 'true')    # Skip if cached
```

---

## 12. Trending Interview Questions & Answers

### 🔵 Basic Level

---

**Q1: What is Azure DevOps and what are its components?**

**Answer:**
> Azure DevOps is Microsoft's cloud-based platform providing end-to-end DevOps tools. Its five core components are:
>
> 1. **Azure Boards** – Agile project management (Kanban boards, sprints, backlogs)
> 2. **Azure Repos** – Git repositories for version control
> 3. **Azure Pipelines** – CI/CD automation for build, test, and deployment
> 4. **Azure Artifacts** – Package management (NuGet, npm, Maven, PyPI)
> 5. **Azure Test Plans** – Manual and automated testing management
>
> Think of it as a **Swiss Army knife** for software delivery — everything you need in one platform.

---

**Q2: What is the difference between CI and CD?**

**Answer:**
```
CI (Continuous Integration):
┌────────────┐     ┌───────┐     ┌──────┐
│ Code Push  │────▶│ Build │────▶│ Test │
└────────────┘     └───────┘     └──────┘
"Every code change is automatically built and tested"

CD (Continuous Delivery):
┌──────┐     ┌──────────────┐     ┌──────────────────┐
│ CI ✅ │────▶│ Deploy to    │────▶│ Ready for Prod   │
│      │     │ Staging      │     │ (Manual Trigger)  │
└──────┘     └──────────────┘     └──────────────────┘
"Code is always in a deployable state, but needs manual approval"

CD (Continuous Deployment):
┌──────┐     ┌──────────────┐     ┌──────────────────┐
│ CI ✅ │────▶│ Deploy to    │────▶│ Auto Deploy to   │
│      │     │ Staging      │     │ Production       │
└──────┘     └──────────────┘     └──────────────────┘
"Every passing change is automatically deployed to production"
```

---

**Q3: What is the difference between Classic Pipelines and YAML Pipelines?**

**Answer:**

| Feature | Classic Pipeline | YAML Pipeline |
|---------|-----------------|---------------|
| **Definition** | GUI-based (click & configure) | Code-based (YAML file in repo) |
| **Version Control** | ❌ Not in repo | ✅ Stored in repo |
| **Portability** | ❌ Tied to project | ✅ Can be shared/templated |
| **Code Review** | ❌ No PR reviews | ✅ Changes reviewed via PRs |
| **Recommendation** | Legacy projects | **✅ All new projects** |

> **Best Practice:** Always use YAML pipelines because they follow "Pipeline as Code" principle — your pipeline definition is version-controlled, reviewable, and reproducible.

---

**Q4: What are Pipeline Agents? Explain the types.**

**Answer:**
> Pipeline agents are **machines (or containers) that run your pipeline jobs**.
>
> **Microsoft-Hosted Agents:**
> - Managed by Microsoft
> - Fresh VM for each job (clean environment)
> - Pre-installed tools (.NET, Node.js, Python, Docker)
> - Limited to 60 minutes per job (free tier)
> - No maintenance required
>
> **Self-Hosted Agents:**
> - You install and manage on your own machines
> - Can be VMs, physical machines, or containers
> - Faster (cached dependencies persist)
> - Access to internal networks/resources
> - Unlimited build time
>
> **When to use Self-Hosted:** When you need private network access, custom tools, or faster builds with cached dependencies.

---

**Q5: What is a Service Connection in Azure DevOps?**

**Answer:**
> A Service Connection is a **secure, authenticated link** between Azure DevOps and an external service. It stores credentials so pipelines can interact with Azure, Docker registries, Kubernetes clusters, etc., **without exposing passwords in code**.
>
> Common types:
> - **Azure Resource Manager** → Deploy to Azure subscriptions
> - **Docker Registry** → Push/pull container images
> - **Kubernetes** → Deploy to K8s clusters
> - **GitHub** → Access external repos

---

### 🟡 Intermediate Level

---

**Q6: What are Environments in Azure Pipelines and why are they important?**

**Answer:**
> Environments represent **deployment targets** (Dev, Staging, Production). They provide:
>
> 1. **Approval Gates** – Require manual approval before deploying to production
> 2. **Deployment History** – Track what was deployed, when, and by whom
> 3. **Resource Tracking** – Associate Kubernetes namespaces, VMs, or App Services
> 4. **Checks & Gates** – Business hours, external API validation, etc.

```yaml
# Environment with approval gate
stages:
  - stage: DeployProd
    jobs:
      - deployment: Deploy
        environment: 'production'    # Configured with approval gate in UI
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying..."
```

```
Pipeline Execution:
Build ✅ → Deploy Dev ✅ → Deploy Staging ✅ → 🔒 APPROVAL → Deploy Prod ✅
                                                     │
                                          Manager approves via
                                          email/Teams notification
```

---

**Q7: How do you handle secrets in Azure DevOps Pipelines?**

**Answer:**
> **Never hardcode secrets!** Use these approaches (from basic to advanced):
>
> 1. **Pipeline Variables (Secret)** – Mark as secret, but limited to the pipeline
> 2. **Variable Groups** – Share secrets across multiple pipelines
> 3. **Azure Key Vault Integration** (✅ Recommended) – Centralized secret management

```yaml
# ❌ WRONG - Never do this!
steps:
  - script: echo "Password is MySecretP@ss123"

# ✅ CORRECT - Use Key Vault
steps:
  - task: AzureKeyVault@2
    inputs:
      azureSubscription: 'MyServiceConnection'
      KeyVaultName: 'my-keyvault'
      SecretsFilter: 'db-password, api-key'

  - script: echo "Using secret securely"
    env:
      DB_PASSWORD: $(db-password)    # Masked in logs automatically
```

---

**Q8: Explain Pipeline Triggers – what types exist?**

**Answer:**

```yaml
# 1. CI Trigger - Runs on code push
trigger:
  branches:
    include:
      - main
      - feature/*
    exclude:
      - experimental/*
  paths:
    include:
      - src/**
    exclude:
      - docs/**

# 2. PR Trigger - Runs on Pull Request
pr:
  branches:
    include:
      - main
  paths:
    include:
      - src/**

# 3. Scheduled Trigger - Runs on a schedule (cron)
schedules:
  - cron: '0 2 * * *'           # Every day at 2 AM UTC
    displayName: 'Nightly Build'
    branches:
      include:
        - main
    always: true                  # Run even if no changes

# 4. Pipeline Trigger - Runs when another pipeline completes
resources:
  pipelines:
    - pipeline: buildPipeline
      source: 'my-build-pipeline'
      trigger:
        branches:
          include:
            - main

# 5. Manual Trigger
trigger: none    # Only run manually
```

---

**Q9: What is the difference between `dependsOn` and `condition` in pipelines?**

**Answer:**

```yaml
stages:
  - stage: Build
    jobs:
      - job: Build
        steps:
          - script: echo "Building..."

  # dependsOn: Defines ORDER (what must finish first)
  - stage: Test
    dependsOn: Build          # Test waits for Build to complete
    jobs:
      - job: Test
        steps:
          - script: echo "Testing..."

  # condition: Defines WHEN to run (adds logic)
  - stage: DeployProd
    dependsOn: Test
    condition: |
      and(
        succeeded(),                                              # Previous stage succeeded
        eq(variables['Build.SourceBranch'], 'refs/heads/main'),  # Only main branch
        ne(variables['Build.Reason'], 'PullRequest')             # Not a PR
      )
    jobs:
      - job: Deploy
        steps:
          - script: echo "Deploying to prod..."
```

```
Think of it this way:
• dependsOn = "Don't start until X finishes" (ordering)
• condition  = "Only run if these rules are true" (logic gate)
```

---

**Q10: How do you implement Blue-Green deployments in Azure DevOps?**

**Answer:**

```
Blue-Green Deployment Strategy:

BEFORE DEPLOYMENT:
┌──────────────────────┐    ┌──────────────────────┐
│    BLUE (v1.0)       │    │    GREEN (idle)       │
│    ✅ LIVE            │    │    ❌ Inactive        │
│    Receiving traffic  │    │                      │
└──────────────────────┘    └──────────────────────┘
         ▲
    Load Balancer / DNS

DURING DEPLOYMENT:
┌──────────────────────┐    ┌──────────────────────┐
│    BLUE (v1.0)       │    │    GREEN (v2.0)       │
│    ✅ LIVE            │    │    🔨 Deploying v2.0  │
│    Receiving traffic  │    │    Testing...         │
└──────────────────────┘    └──────────────────────┘

AFTER SWAP:
┌──────────────────────┐    ┌──────────────────────┐
│    BLUE (v1.0)       │    │    GREEN (v2.0)       │
│    ❌ Standby         │    │    ✅ LIVE (swapped!) │
│    (Rollback ready)   │    │    Receiving traffic  │
└──────────────────────┘    └──────────────────────┘
                                     ▲
                                Load Balancer / DNS
```

```yaml
# Azure App Service Slot Swap (Blue-Green)
steps:
  # Deploy to staging slot (Green)
  - task: AzureWebApp@1
    inputs:
      azureSubscription: 'AzureConnection'
      appName: 'my-production-app'
      deployToSlotOrASE: true
      slotName: 'staging'
      package: '$(Pipeline.Workspace)/drop/**'

  # Run smoke tests against staging slot
  - script: |
      curl -f https://my-production-app-staging.azurewebsites.net/health
    displayName: 'Smoke Test on Staging Slot'

  # Swap staging ↔ production (zero downtime!)
  - task: AzureAppServiceManage@0
    inputs:
      azureSubscription: 'AzureConnection'
      action: 'Swap Slots'
      webAppName: 'my-production-app'
      resourceGroupName: 'production-rg'
      sourceSlot: 'staging'
```

---

### 🔴 Advanced Level

---

**Q11: How do you implement a multi-repo pipeline that builds microservices?**

**Answer:**

```yaml
# Main pipeline that references multiple repos
resources:
  repositories:
    - repository: frontend-repo
      type: git
      name: myproject/frontend
      ref: refs/heads/main

    - repository: backend-repo
      type: git
      name: myproject/backend-api
      ref: refs/heads/main

    - repository: shared-libs
      type: git
      name: myproject/shared-libraries
      ref: refs/heads/main

trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

steps:
  # Checkout main repo (self)
  - checkout: self

  # Checkout frontend repo
  - checkout: frontend-repo
    path: 'frontend'

  # Checkout backend repo
  - checkout: backend-repo
    path: 'backend'

  # Checkout shared libraries
  - checkout: shared-libs
    path: 'shared'

  - script: |
      echo "Building frontend from $(Build.SourcesDirectory)/frontend"
      echo "Building backend from $(Build.SourcesDirectory)/backend"
    displayName: 'Build all microservices'
```

---

**Q12: How do you implement rollback strategies in Azure Pipelines?**

**Answer:**

```yaml
# Strategy 1: Re-deploy previous artifact
stages:
  - stage: Rollback
    displayName: 'Rollback to Previous Version'
    condition: failed()    # Only if deployment failed
    jobs:
      - deployment: Rollback
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                # Download previous successful artifact
                - task: DownloadPipelineArtifact@2
                  inputs:
                    buildType: 'specific'
                    project: '$(System.TeamProjectId)'
                    pipeline: '$(System.DefinitionId)'
                    buildVersionToDownload: 'latestFromBranch'
                    branchName: 'refs/heads/main'
                    artifactName: 'drop'
                    targetPath: '$(Pipeline.Workspace)/rollback'

                - task: AzureWebApp@1
                  inputs:
                    azureSubscription: 'AzureConnection'
                    appName: 'my-production-app'
                    package: '$(Pipeline.Workspace)/rollback/**'
                  displayName: '⏪ Rollback Deployment'

# Strategy 2: Slot Swap Back (for App Services)
  - stage: RollbackSlot
    jobs:
      - job: SwapBack
        steps:
          - task: AzureAppServiceManage@0
            inputs:
              action: 'Swap Slots'
              webAppName: 'my-production-app'
              sourceSlot: 'staging'    # Swap back to old version
```

---

**Q13: Explain how to set up a complete Git workflow with branch policies in Azure DevOps.**

**Answer:**

```
Recommended Git Workflow (GitFlow):

                     main (protected)
                       │
           ┌───────────┼───────────┐
           │           │           │
     hotfix/fix1   release/1.0  develop (protected)
                                   │
                    ┌──────────────┼──────────────┐
                    │              │              │
              feature/login  feature/cart  feature/search
```

**Branch Policies for `main`:**
```
┌─────────────────────────────────────────────────────┐
│  Branch Policy: main                                 │
│                                                       │
│  ✅ Minimum 2 reviewers required                     │
│  ✅ Requestor cannot approve their own changes       │
│  ✅ All comments must be resolved                    │
│  ✅ Build validation must pass                       │
│     → Pipeline: CI-Build-Pipeline                    │
│     → Trigger: Automatic                             │
│     → Policy requirement: Required                   │
│  ✅ Linked work items required                       │
│  ✅ Merge strategy: Squash merge only                │
│  ✅ Automatically delete source branch after merge   │
│  ✅ Require approval from code owners                │
│     → /src/api/** → @backend-team                    │
│     → /src/frontend/** → @frontend-team              │
│     → /infrastructure/** → @devops-team              │
└─────────────────────────────────────────────────────┘
```

---

**Q14: How do you optimize pipeline performance?**

**Answer:**

```yaml
# Optimization 1: Parallel Jobs
stages:
  - stage: Build
    jobs:
      - job: Frontend          # These run in PARALLEL
        steps:
          - script: npm run build
      - job: Backend           # if they don't depend
        steps:                 # on each other
          - script: dotnet build
      - job: MobileApp
        steps:
          - script: flutter build

# Optimization 2: Caching
steps:
  - task: Cache@2
    inputs:
      key: 'npm | "$(Agent.OS)" | package-lock.json'
      path: 'node_modules'
      cacheHitVar: 'CACHE_HIT'
  - script: npm ci
    condition: ne(variables.CACHE_HIT, 'true')

# Optimization 3: Path Filters (skip unnecessary builds)
trigger:
  paths:
    include:
      - src/**               # Only build when source code changes
    exclude:
      - docs/**              # Don't build for docs changes
      - '**/*.md'            # Don't build for README changes

# Optimization 4: Incremental Builds
steps:
  - task: DotNetCoreCLI@2
    inputs:
      command: 'build'
      arguments: '--no-restore'    # Skip restore if already cached

# Optimization 5: Use Container Jobs (pre-built images)
jobs:
  - job: Build
    container: node:18-alpine      # Skip NodeTool installation
    steps:
      - script: npm ci && npm run build
```

**Performance Comparison:**
```
Before Optimization:           After Optimization:
┌─────────────────────┐       ┌─────────────────────┐
│ Install Node   2min │       │ Cache hit!     10sec │
│ npm install    3min │       │ npm ci (cached) 30sec│
│ Lint           1min │       │ Lint + Test     1min │
│ Test           2min │       │ (parallel)           │
│ Build          2min │       │ Build          1.5min│
│ Deploy         2min │       │ Deploy         1.5min│
├─────────────────────┤       ├─────────────────────┤
│ Total:       12 min │       │ Total:       4.5 min │
└─────────────────────┘       └─────────────────────┘
                              63% faster! 🚀
```

---

**Q15: How do you implement Infrastructure as Code (IaC) with Azure DevOps?**

**Answer:**

```
┌──────────────────────────────────────────────────────────┐
│               IaC PIPELINE FLOW                           │
│                                                           │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐           │
│  │ Terraform │──▶│ terraform│──▶│ terraform│           │
│  │ files in  │   │  plan    │   │  apply   │           │
│  │ Azure     │   │ (review) │   │ (deploy) │           │
│  │ Repos     │   │          │   │          │           │
│  └──────────┘    └──────────┘    └──────────┘           │
│       │               │               │                  │
│       │          ┌─────┴─────┐   ┌────┴─────┐           │
│       │          │ Plan saved │   │ 🔒 Manual │           │
│       │          │ as artifact│   │  Approval │           │
│       │          └───────────┘   └──────────┘           │
│                                                           │
│  State File: Stored in Azure Storage Account (Remote)    │
└──────────────────────────────────────────────────────────┘
```

```hcl
# infrastructure/main.tf
terraform {
  required_version = ">= 1.0"

  backend "azurerm" {
    resource_group_name  = "tf-state-rg"
    storage_account_name = "tfstatestorage"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}

provider "azurerm" {
  features {}
}

# Resource Group
resource "azurerm_resource_group" "main" {
  name     = "ecommerce-${var.environment}-rg"
  location = var.location
}

# App Service Plan
resource "azurerm_service_plan" "main" {
  name                = "ecommerce-${var.environment}-plan"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  os_type             = "Linux"
  sku_name            = var.environment == "production" ? "P1v3" : "B1"
}

# Web App
resource "azurerm_linux_web_app" "main" {
  name                = "ecommerce-${var.environment}-app"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  service_plan_id     = azurerm_service_plan.main.id

  site_config {
    application_stack {
      node_version = "18-lts"
    }
  }

  app_settings = {
    "ENVIRONMENT" = var.environment
  }
}
```

---

**Q16: What are Pipeline Decorators and when would you use them?**

**Answer:**
> Pipeline Decorators are **organization-wide injections** that automatically add steps to every pipeline. They're used for enforcing security/compliance across all projects without modifying individual pipelines.
>
> **Use Cases:**
> - Security scanning (inject vulnerability scanner into every build)
> - Compliance checks (ensure every deployment logs an audit trail)
> - Cost tracking (add resource usage tagging to every deployment)
>
> **Note:** Decorators require creating an Azure DevOps extension (advanced feature).

---

**Q17: How do you handle database migrations in CI/CD pipelines?**

**Answer:**

```yaml
stages:
  - stage: DatabaseMigration
    displayName: '🗄️ Database Migration'
    jobs:
      - deployment: Migrate
        environment: 'production-db'    # Separate approval for DB changes
        strategy:
          runOnce:
            deploy:
              steps:
                # For Entity Framework (.NET)
                - task: DotNetCoreCLI@2
                  displayName: 'Install EF Tools'
                  inputs:
                    command: custom
                    custom: tool
                    arguments: 'install --global dotnet-ef'

                - script: |
                    dotnet ef database update \
                      --project src/DataAccess \
                      --connection "$(DB_CONNECTION_STRING)"
                  displayName: 'Run Database Migration'

                # For Flyway (Java)
                # - script: |
                #     flyway -url=$(DB_URL) -user=$(DB_USER) \
                #       -password=$(DB_PASSWORD) migrate

                # For Node.js (Knex)
                # - script: npx knex migrate:latest
```

```
Migration Safety Pattern:
┌─────────────────────────────────────────────────┐
│                                                   │
│  1. Backup Database        (automated)           │
│  2. Run Migration          (automated)           │
│  3. Verify Migration       (automated health     │
│                             check)               │
│  4. Deploy New App Code    (automated)           │
│  5. Verify Application     (smoke test)          │
│  6. If failed → Rollback   (automated or         │
│     migration + restore     manual)              │
│     backup                                        │
│                                                   │
└─────────────────────────────────────────────────┘
```

---

**Q18: Explain the concept of "Shift Left" in Azure DevOps context.**

**Answer:**

```
Traditional Approach (Shift Right - Problems found LATE):

Development ──▶ Testing ──▶ Staging ──▶ Production
                                             │
                                        🐛 Bugs found here!
                                        💰 Expensive to fix!

"Shift Left" Approach (Problems found EARLY):

Development ──▶ Testing ──▶ Staging ──▶ Production
     │
🐛 Bugs found here!
💰 Cheap to fix!

How to implement in Azure DevOps:
┌─────────────────────────────────────────────────────────────┐
│  1. Pre-commit hooks     → Lint code before committing     │
│  2. PR Build Validation  → Run tests on every PR           │
│  3. SAST Scanning        → Scan for vulnerabilities early  │
│  4. Unit Tests           → Test at the smallest unit level │
│  5. SonarQube Quality    → Enforce code quality gates      │
│     Gates                                                    │
│  6. Container Scanning   → Check images before deployment  │
│  7. Dependency Scanning  → Check for vulnerable packages   │
└─────────────────────────────────────────────────────────────┘
```

---

**Q19: How do you set up monitoring and notifications for pipelines?**

**Answer:**

```yaml
# Option 1: Built-in Notifications
# Go to: Project Settings → Notifications → New Subscription
# Events: Build completed, Build failed, PR created, etc.
# Channels: Email, Microsoft Teams, Slack (via extensions)

# Option 2: Teams/Slack Webhook in Pipeline
steps:
  - script: |
      curl -H 'Content-Type: application/json' \
           -d '{
                 "text": "✅ Build $(Build.BuildNumber) succeeded!",
                 "@type": "MessageCard",
                 "summary": "Pipeline Notification"
               }' \
           $(TEAMS_WEBHOOK_URL)
    displayName: 'Notify Teams on Success'
    condition: succeeded()

  - script: |
      curl -H 'Content-Type: application/json' \
           -d '{
                 "text": "❌ Build $(Build.BuildNumber) FAILED!",
                 "@type": "MessageCard",
                 "summary": "Pipeline Failure"
               }' \
           $(TEAMS_WEBHOOK_URL)
    displayName: 'Notify Teams on Failure'
    condition: failed()
```

---

**Q20: Compare Azure DevOps with GitHub Actions. When would you choose one over the other?**

**Answer:**

| Feature | Azure DevOps | GitHub Actions |
|---------|-------------|----------------|
| **Best For** | Enterprise, complex projects | Open source, simpler workflows |
| **Project Management** | ✅ Azure Boards (built-in) | ❌ Need separate tool |
| **Repos** | Azure Repos (Git + TFVC) | GitHub Repos (Git only) |
| **Pipeline Definition** | YAML or Classic GUI | YAML only |
| **Marketplace** | 1000+ extensions | 15000+ actions |
| **Self-Hosted Agents** | ✅ Full support | ✅ Self-hosted runners |
| **Deployment Environments** | ✅ Advanced (approval gates, checks) | ✅ Basic (approval gates) |
| **Artifacts** | ✅ Built-in feed management | ✅ GitHub Packages |
| **Test Plans** | ✅ Manual + Exploratory testing | ❌ No built-in test management |
| **Enterprise Features** | ✅ RBAC, Audit logs, Compliance | ✅ GHEC (Enterprise Cloud) |
| **Pricing** | Free for 5 users, then pay | Free for public repos |
| **Integration with Azure** | ✅ Native, seamless | ✅ Good (via actions) |
| **Integration with GitHub** | ✅ Good | ✅ Native, seamless |

> **Choose Azure DevOps when:**
> - You need full ALM (Application Lifecycle Management)
> - Enterprise with complex approval workflows
> - Using Azure-heavy infrastructure
> - Need built-in test plan management
> - Team already uses Microsoft ecosystem
>
> **Choose GitHub Actions when:**
> - Open-source projects
> - Code is already on GitHub
> - Simpler CI/CD workflows
> - Want larger marketplace of pre-built actions
> - Startup or small team

---

## 13. Cheat Sheet

### Pipeline Variables Quick Reference

```yaml
# ═══════════════════════════════════════════
# PREDEFINED VARIABLES (System variables)
# ═══════════════════════════════════════════

$(Build.BuildId)              # Unique build ID (e.g., 1234)
$(Build.BuildNumber)          # Build number (e.g., 20240115.1)
$(Build.SourceBranch)         # Full branch name (refs/heads/main)
$(Build.SourceBranchName)     # Short branch name (main)
$(Build.Repository.Name)      # Repo name
$(Build.SourcesDirectory)     # Path to source code
$(Build.ArtifactStagingDirectory)  # Path for artifacts
$(Build.Reason)               # Why build ran (Manual, IndividualCI, PullRequest)

$(System.DefaultWorkingDirectory)  # Working directory
$(Agent.OS)                   # Agent OS (Linux, Windows_NT, Darwin)
$(Agent.TempDirectory)        # Temp directory

$(Pipeline.Workspace)         # Root workspace directory

# ═══════════════════════════════════════════
# CUSTOM VARIABLES
# ═══════════════════════════════════════════

# Inline
variables:
  myVar: 'hello'

# Variable Group (from Library)
variables:
  - group: 'my-variable-group'

# Template Variable
variables:
  - template: variables/dev.yml

# Secret (set in UI, reference in YAML)
# $(my-secret-var) → set as secret in pipeline settings

# Runtime expression
variables:
  fullTag: $[format('{0}-{1}', variables['Build.SourceBranchName'], variables['Build.BuildId'])]
```

### Common Tasks Quick Reference

```yaml
# ─── .NET ───
- task: UseDotNet@2                    # Install .NET SDK
- task: DotNetCoreCLI@2                # Build, Test, Publish
- task: NuGetCommand@2                 # NuGet operations

# ─── Node.js ───
- task: NodeTool@0                     # Install Node.js
- script: npm ci                       # Install packages
- script: npm test                     # Run tests
- script: npm run build                # Build

# ─── Docker ───
- task: Docker@2                       # Build, Push images

# ─── Azure ───
- task: AzureWebApp@1                  # Deploy to App Service
- task: AzureKeyVault@2                # Fetch Key Vault secrets
- task: AzureCLI@2                     # Run Azure CLI commands
- task: AzureAppServiceManage@0        # Manage App Service (swap slots)

# ─── Kubernetes ───
- task: KubernetesManifest@0           # Deploy K8s manifests
- task: Kubernetes@1                   # Run kubectl commands
- task: HelmDeploy@0                   # Helm chart deployment

# ─── Terraform ───
- task: TerraformInstaller@0           # Install Terraform
- task: TerraformTaskV4@4              # Init, Plan, Apply

# ─── Testing ───
- task: PublishTestResults@2           # Publish test results
- task: PublishCodeCoverageResults@1   # Publish coverage

# ─── Artifacts ───
- task: PublishBuildArtifacts@1        # Upload artifact
- task: DownloadBuildArtifacts@0       # Download artifact
- task: PublishPipelineArtifact@1      # Publish pipeline artifact
- task: DownloadPipelineArtifact@2     # Download pipeline artifact

# ─── Utilities ───
- task: Cache@2                        # Cache dependencies
- task: CopyFiles@2                    # Copy files
- task: ArchiveFiles@2                 # Zip/tar files
- script: |                            # Run shell commands
    echo "Hello"
- powershell: |                        # Run PowerShell
    Write-Host "Hello"
```

### Azure CLI Commands Cheat Sheet

```bash
# ═══ Azure DevOps CLI ═══

# Install extension
az extension add --name azure-devops

# Login and set defaults
az devops configure --defaults organization=https://dev.azure.com/myorg project=myproject

# List pipelines
az pipelines list --output table

# Run a pipeline
az pipelines run --name "My-Pipeline" --branch main

# List builds
az pipelines build list --top 10 --output table

# Create variable group
az pipelines variable-group create --name "my-vars" --variables key1=value1 key2=value2

# Create service connection
az devops service-endpoint azurerm create \
  --name "AzureConnection" \
  --azure-rm-service-principal-id "xxx" \
  --azure-rm-subscription-id "xxx" \
  --azure-rm-tenant-id "xxx"

# Create work item
az boards work-item create --type "User Story" --title "Add login feature"
```

### Common Pipeline Patterns

```yaml
# ═══ Pattern: Deploy to Multiple Environments ═══
stages:
  - stage: Build
  - stage: DeployDev
    dependsOn: Build
    condition: succeeded()
  - stage: DeployStaging
    dependsOn: DeployDev
    condition: succeeded()
  - stage: DeployProd
    dependsOn: DeployStaging
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))

# ═══ Pattern: Fan-out / Fan-in ═══
stages:
  - stage: Build
  - stage: DeployUS                    # These run
    dependsOn: Build                   # in PARALLEL
  - stage: DeployEU                    # (fan-out)
    dependsOn: Build
  - stage: DeployAsia
    dependsOn: Build
  - stage: VerifyAll                   # This waits for ALL
    dependsOn:                         # (fan-in)
      - DeployUS
      - DeployEU
      - DeployAsia

# ═══ Pattern: Canary Deployment ═══
stages:
  - stage: CanaryDeploy
    jobs:
      - deployment: Canary
        environment: 'production'
        strategy:
          canary:
            increments: [10, 20]       # Deploy to 10%, then 20%
            preDeploy:
              steps:
                - script: echo "Preparing canary..."
            deploy:
              steps:
                - script: echo "Deploying canary..."
            routeTraffic:
              steps:
                - script: echo "Routing $(strategy.increment)% traffic..."
            postRouteTraffic:
              steps:
                - script: echo "Monitoring health..."
            on:
              success:
                steps:
                  - script: echo "Canary successful! Rolling out to 100%"
              failure:
                steps:
                  - script: echo "Canary failed! Rolling back..."
```

---

## 📚 Quick Reference Links

| Resource | URL |
|----------|-----|
| Azure DevOps Documentation | [docs.microsoft.com/azure/devops](https://docs.microsoft.com/azure/devops) |
| YAML Schema Reference | [docs.microsoft.com/azure/devops/pipelines/yaml-schema](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema) |
| Predefined Variables | [docs.microsoft.com/azure/devops/pipelines/build/variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables) |
| Task Reference | [docs.microsoft.com/azure/devops/pipelines/tasks](https://docs.microsoft.com/azure/devops/pipelines/tasks) |
| Azure DevOps REST API | [docs.microsoft.com/rest/api/azure/devops](https://docs.microsoft.com/rest/api/azure/devops) |
| Azure DevOps CLI | [docs.microsoft.com/azure/devops/cli](https://docs.microsoft.com/azure/devops/cli) |
| Marketplace | [marketplace.visualstudio.com](https://marketplace.visualstudio.com) |

---

## 🎓 Learning Path

```
Beginner (Week 1-2):
├── Create Azure DevOps account
├── Create first project
├── Push code to Azure Repos
├── Create basic CI pipeline
└── Understand YAML syntax

Intermediate (Week 3-4):
├── Multi-stage pipelines
├── Variable groups & Key Vault
├── Branch policies & PR workflows
├── Docker build & push
├── Deploy to Azure App Service
└── Azure Artifacts feeds

Advanced (Week 5-8):
├── Template pipelines
├── Self-hosted agents
├── Terraform/IaC pipelines
├── Kubernetes deployments
├── Blue-Green & Canary strategies
├── Pipeline optimization & caching
├── Security scanning integration
└── Multi-repo pipelines
```

---

> **💡 Pro Tip:** The best way to learn Azure DevOps is to **build a real project end-to-end**. Start with a simple web app, set up CI/CD, and gradually add complexity (Docker, Kubernetes, Terraform, monitoring).

---

*Last updated: 2024 | Created for DevOps Engineers transitioning to or working with Azure DevOps*
