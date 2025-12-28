# 🚀 GitOps Configuration Repository

Kubernetes manifests for the [House Price Prediction API](https://github.com/Tomo1912/my-ml-model-repo), managed by ArgoCD.

---

## 🎯 Purpose

This repository serves as the **single source of truth** for the Kubernetes cluster state. ArgoCD continuously monitors this repo and automatically deploys any changes to the K3s cluster.

---

## 📁 Contents

| File | Description |
|------|-------------|
| `deployment.yaml` | Kubernetes Deployment + Service manifest |

---

## 🔄 How It Works

```
my-ml-model-repo (code change)
         ↓
GitHub Actions builds & pushes Docker image
         ↓
CI pipeline updates image tag in this repo
         ↓
ArgoCD detects change (polling every 3 min)
         ↓
Auto-deploy to K3s cluster
```

---

## 🛠️ Infrastructure

| Component | Technology |
|-----------|------------|
| **Cluster** | K3s on Hetzner VPS |
| **GitOps** | ArgoCD |
| **Proxy** | Traefik |
| **SSL** | Let's Encrypt |
| **Domain** | ml-house-price.duckdns.org |

---

## 🔗 Related Repository

- **Application code:** [my-ml-model-repo](https://github.com/Tomo1912/my-ml-model-repo)

