# Todo Project Config

# 4.10 - The Project, the grande finale

## Task

> Finalize the Project setup so that it uses different repositories for the code and configurations.

## Implementation

The Todo Project was reorganized into two separate GitHub repositories to separate application source code from Kubernetes configurations.

**1. Application Repository**

[**Kubernetes-MOOC-2026**](https://github.com/Viktoriia-code/Kubernetes-MOOC-2026)

Contains:
- Source code for Todo App, Todo Backend, and Todo Broadcaster.
- Dockerfiles and application dependencies.
- GitHub Actions workflows for building and publishing Docker images to Docker Hub.

**2. Configuration Repository**

[**todo-project-config**](https://github.com/Viktoriia-code/todo-project-config)

Contains:
- Kubernetes manifests for the applications and supporting services.
- Kustomize base configurations and overlays.
- Separate staging and production environments.
- Argo CD Application definitions.
- Argo CD Image Updater configuration.

## Repository Structure

```text
todo-project-config/
├── environments/
│   ├── base/
│   │   ├── kustomization.yaml
│   │   ├── todo_app/
│   │   │   ├── kustomization.yaml
│   │   │   └── manifests/
│   │   ├── todo_backend/
│   │   │   ├── kustomization.yaml
│   │   │   └── manifests/
│   │   └── todo_broadcaster/
│   │       ├── kustomization.yaml
│   │       └── manifests/
│   └── overlays/
│       ├── staging/
│       └── production/
├── argocd/
│   └── applications/
│       ├── staging.yaml
│       └── production.yaml
└── image-updater.yaml
```

## GitOps Workflow

1. Application source code is maintained in the original repository.
2. GitHub Actions builds and pushes Docker images to Docker Hub.
3. Kubernetes configurations are maintained in the separate configuration repository.
4. Kustomize manages environment specific configurations for staging and production.
5. Argo CD synchronizes Kubernetes resources from the configuration repository.
6. Argo CD Image Updater monitors Docker images and updates image references using the configured update strategies.

## Argo CD Configuration

Both staging and production applications reference the configuration repository.

**Staging:**

```yaml
source:
  repoURL: https://github.com/Viktoriia-code/todo-project-config.git
  path: environments/overlays/staging
  targetRevision: main
```

**Production:**

```yaml
source:
  repoURL: https://github.com/Viktoriia-code/todo-project-config.git
  path: environments/overlays/production
  targetRevision: main
```

## Configuration Validation

Validate both environments using Kustomize:

```powershell
kubectl kustomize .\environments\overlays\staging
kubectl kustomize .\environments\overlays\production
```

Validate Kubernetes resources without applying changes:

```powershell
kubectl apply --dry-run=client -k .\environments\overlays\staging
kubectl apply --dry-run=client -k .\environments\overlays\production
```

Apply the Argo CD Application configurations:

```powershell
kubectl apply -f .\argocd\applications\
```

Verify application synchronization:

```powershell
kubectl get applications -n argocd
```

Verify that both applications reference the new repository:

```powershell
kubectl get applications -n argocd -o custom-columns="NAME:.metadata.name,REPO:.spec.source.repoURL,REVISION:.spec.source.targetRevision"
```
<img width="773" height="auto" alt="image" src="https://github.com/user-attachments/assets/6d1c32b7-1fa4-4c63-9cc2-e1f5f5375cab" />

## Result

The Todo Project source code and Kubernetes configurations are maintained in separate repositories.

<img width="842" height="auto" alt="image" src="https://github.com/user-attachments/assets/5dd4a171-6fb6-4cc8-b777-7daae733196b" />

This separation allows application development and deployment configurations to be managed independently while supporting automated deployments through GitHub Actions, Kustomize, Argo CD, and Argo CD Image Updater.
