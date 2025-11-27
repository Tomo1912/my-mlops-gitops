# 🧠 GitOps MLOps System: Automated Deployment on K3s 🚀

This project showcases an end-to-end DevOps (MLOps) automation solution for machine learning model serving, based on the **GitOps philosophy**.

The goal was to establish a reliable Continuous Integration / Continuous Deployment (CI/CD) pipeline where Git is the **single source of truth** for the state of the Kubernetes cluster, ensuring continuous deployment of new model versions.

---

## 🗺️ Solution Architecture

The system ensures that any change in the application code automatically results in a deployment of the model's new version onto the cluster.

| Component | Technology | Role in the Pipeline |
| :--- | :--- | :--- |
| **Cluster** | K3s (Kubernetes) | Lightweight K8s used for hosting containers on a Hetzner VPS. |
| **GitOps Controller** | ArgoCD | Continuously monitors the GitOps repository and ensures Self-Healing. |
| **CI/CD** | GitHub Actions | Automates the Docker image build, push, and K8s manifest patching. |
| **Registry** | GHCR (GitHub Container Registry) | Storage for the model's Docker images. |
| **Application** | FastAPI + Python | Model serving (exposed permanently on NodePort 32199). |

### Workflow (The CI/CD Loop)

1.  **Commit:** Code changes are pushed to the `my-ml-model-repo`.
2.  **Build:** GitHub Actions successfully builds the Docker image and tags it with the commit's SHA.
3.  **CD Trigger:** GitHub Actions automatically updates the image tag in the K8s manifest located in the dedicated `my-mlops-gitops` repository.
4.  **Deployment:** ArgoCD detects the change in the GitOps repository and automatically initiates the deployment of the new container onto the K3s cluster.

## 🛡️ Key Engineering Challenges Solved
The following critical challenges were successfully addressed during implementation, proving system resilience:

1.  **Permanent Service Exposure (Model & UI):** We eliminated temporary tunnelling (`kubectl port-forward`) by configuring **two permanent NodePort Services** directly within the Git manifest:
    * **ML Model Access:** Exposed on port **32199**.
    * **ArgoCD UI Access:** Exposed on port **30081**.
2.  **Authentication:** Solved the persistent **`ErrImagePull`** error by applying an **Image Pull Secret** (derived from a GitHub PAT) to the K8s cluster, ensuring secure image access from GHCR.
3.  **Self-Healing Stability:** Implemented logic ensures that ArgoCD does not immediately revert critical configuration changes (like changing the Service Type from ClusterIP to NodePort) because the **Source of Truth** (Git repository) was permanently fixed to the required configuration, resolving issues related to the NodePort Service being unintentionally deleted or reverted.

### 🔗 Accessing the Live Application

The application is running on NodePort 32199. Replace `X.X.X.X` with your public IP address.

```bash
# Example check of the running model
curl http://X.X.X.X:32199/predict/21
# -> {"input":21, "prediction":48, "model_version":"v1.1.0"}

