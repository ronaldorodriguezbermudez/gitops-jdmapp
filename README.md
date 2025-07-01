# 🚀 GitOps JDM App

This repository implements a GitOps strategy for automated deployment of the JDM application using Flux CD on Kubernetes.

##  What does this repository do?

This repository contains Infrastructure as Code (IaC) configuration to automatically manage the deployment of the `jdmapp` application on a Kubernetes cluster using the GitOps pattern.

### ⭐ Key Features:

- **🔄 GitOps Workflow**: Uses Flux CD to automatically sync cluster state with code in this repository
- **📦 Helm Charts Management**: Deploys the application using Helm charts from an external repository
- **🌍 Environment Configuration**: Organized structure by environments (currently staging)
- **⚡ Automated Deployment**: Changes in this repository are automatically reflected in the cluster

## 📁 Repository Structure

```
├── README.md
└── clusters/
    └── staging/
        └── myapp/
            ├── HelmRelease.yaml      # Helm release configuration
            ├── HelmRepository.yaml   # Helm charts repository
            ├── kustomization.yaml    # Kustomize configuration
            └── flux-system/
                ├── gotk-components.yaml  # Flux components
                ├── gotk-sync.yaml        # Sync configuration
                └── kustomization.yaml    # Kustomize for Flux
```

## ⚙️ Current Configuration

### 🎯 JDM Application
- **📛 Name**: jdmapp
- **🏷️ Current Version**: 0.1.36
- **🏠 Namespace**: jdmapp
- **📊 Helm Repository**: https://ronaldorodriguezbermudez.github.io/helm-chart-app/

### 🌊 Flux CD
- **⏰ Sync Interval**: 1 minute
- **🌲 Monitored Branch**: main
- **📂 Path**: `./clusters/staging/myapp`

## 🔄 How it works

1. **👨‍💻 Development**: Developers make changes to the application code
2. **🏗️ CI/CD**: The CI/CD pipeline builds a new image and updates the Helm chart
3. **🔄 GitOps**: Flux CD detects changes in this repository every minute
4. **🚀 Deployment**: Flux automatically applies changes to the Kubernetes cluster
5. **📊 Monitoring**: Flux maintains the desired state and reports health status

## 📥 Installation in Your Cluster

To use this GitOps setup in your own Kubernetes cluster, follow these steps:

### 📋 Prerequisites

- ☸️ Kubernetes cluster (v1.21+)
- 🛠️ `kubectl` configured to access your cluster
- 🌊 `flux` CLI installed ([Installation guide](https://fluxcd.io/flux/installation/))
- 🔐 Git repository access (SSH or HTTPS)

### 1️⃣ Install Flux CD

```bash
# Check prerequisites
flux check --pre

# Install Flux in your cluster
flux bootstrap github \
  --owner=YOUR_GITHUB_USERNAME \
  --repository=gitops-jdmapp \
  --branch=main \
  --path=./clusters/staging/myapp \
  --personal
```

### 2️⃣ Fork and Configure

1. **🍴 Fork this repository** to your GitHub account

2. **📝 Update the Git repository URL** in `clusters/staging/myapp/flux-system/gotk-sync.yaml`:
   ```yaml
   spec:
     url: ssh://git@github.com/YOUR_USERNAME/gitops-jdmapp
   ```

3. **🔧 Configure your Helm repository** (optional):
   - Update `clusters/staging/myapp/HelmRepository.yaml` if you want to use your own Helm repository
   - Or keep the existing one to deploy the JDM app

### 3️⃣ Apply Configuration

```bash
# Clone your forked repository
git clone git@github.com:YOUR_USERNAME/gitops-jdmapp.git
cd gitops-jdmapp

# Apply the Flux configuration
kubectl apply -k clusters/staging/myapp/flux-system/

# Verify Flux installation
flux get all
```

### 4️⃣ Deploy the Application

```bash
# Apply the application configuration
kubectl apply -k clusters/staging/myapp/

# Check the deployment status
kubectl get pods -n jdmapp
kubectl get helmrelease -n flux-system
```

### 5️⃣ Verify Everything is Working

```bash
# Check Flux components
flux get sources git
flux get kustomizations
flux get helmreleases

# Check application pods
kubectl get pods -n jdmapp

# Check application service (if exposed)
kubectl get svc -n jdmapp
```

### 🎨 Customization

To adapt this for your own application:

1. **📊 Update HelmRepository.yaml** with your Helm chart repository
2. **📝 Modify HelmRelease.yaml** with your application name and version
3. **🏠 Adjust namespace** if needed
4. **⏰ Update sync interval** based on your requirements

## ✏️ Making Changes

To update the application:

1. **📝 Modify the version** in `HelmRelease.yaml`:
   ```yaml
   spec:
     chart:
       spec:
         version: "NEW_VERSION"
   ```

2. **💾 Commit and push the changes**:
   ```bash
   git add .
   git commit -m "Update jdmapp to version X.X.X"
   git push origin main
   ```

3. **🔄 Flux will automatically detect** the changes and update the cluster

## 📊 Monitoring

To check deployment status:

```bash
# View Flux status
flux get all

# View Flux logs
flux logs

# View HelmRelease status
kubectl get helmrelease -n flux-system
```

## 🌟 GitOps Benefits

- **📋 Traceability**: All changes are recorded in Git
- **⏪ Reversibility**: Easy rollback to previous versions
- **🤖 Automation**: Deployments without manual intervention
- **🔄 Consistency**: Cluster state always reflects the code
- **🔐 Security**: Controlled access through Git
