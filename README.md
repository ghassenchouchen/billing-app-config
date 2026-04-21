# PFE App - GitOps Config Repo

This repository holds the **Kubernetes desired state** for the billing pfeapplication. It is:
- **Updated by Jenkins** after every successful CI build (image tag changes)
- **Observed by ArgoCD** which automatically deploys changes to the EKS cluster

## Structure

```
├── charts/
│   ├── backend/           # Shared Helm chart for all 8 Spring Boot microservices
│   │   ├── Chart.yaml
│   │   ├── values.yaml    # Shared defaults (probes, resources, namespace)
│   │   ├── templates/
│   │   │   └── deployment.yaml   
│   │   └── services/
│   │       ├── api-gateway/values.yaml
│   │       ├── authentication-service/values.yaml
│   │       ├── billing-service/values.yaml
│   │       ├── boutique-service/values.yaml
│   │       ├── catalog-service/values.yaml
│   │       ├── customer-service/values.yaml
│   │       ├── subscription-service/values.yaml
│   │       └── usage-service/values.yaml
│   └── frontend/          # Helm chart for the Angular frontend
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           └── deployment.yaml
├── infra
│   ├── configmap.yaml         #shared environment config (DB urls)
│   ├── ingress.yaml           #NGINX ingress routing rules   
│   ├── kafka.yaml             #kafka service & statefulset configuration
│   └── sealedsecret.yaml      #Encrypted sensitive information
```



After a successful build, Jenkins:
1. Clones this repository
2. Updates the `image.tag` field in the `services/<name>/values.yaml` #Example: service/authentication-service/values.yaml
3. Commits and pushes the change

ArgoCD detects the new commit and automatically rolls out the updated image to EKS.
