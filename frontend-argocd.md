
roboshop frontend argocd deployment:
-----------------------------------

Please clone below repository frontend-argocd 

`https://github.com/iam-vanimina/frontend-argocd.git `



`cd  /frontend-argocd`

Make changes helm values as per your tags or version or image url etc ..

Make changes in application.yaml (mention your github repo url and create k8s roboshop namespace )

`kubectl apply -f application.yaml  `

In my case my github repo is 

`https://github.com/iam-vanimina/frontend-argocd.git `

github repo act as the truth for the argocd.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Initially we have created docker desktop Kubernetes with worker nodes.

<img width="938" height="485" alt="image" src="https://github.com/user-attachments/assets/f89a4a95-eeca-40db-8f9d-33c2501652e7" />


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------


`kubectl get nodes `



<img width="420" height="75" alt="image" src="https://github.com/user-attachments/assets/b2015c93-baa7-420c-bd5b-262f53142a94" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
view the information of nodes 

`kubectl get node -o wide `

<img width="951" height="107" alt="image" src="https://github.com/user-attachments/assets/06c5cf1d-790a-4f5e-a89f-1deddd4aa0ca" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

view the information of all pods in roboshop namespace

 `kubectl get pods -n roboshop`

 <img width="387" height="212" alt="image" src="https://github.com/user-attachments/assets/3dfdd951-97c7-4730-9807-657b280a28d2" />


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

view the information of all services in roboshop namespace

`kubectl get svc -n roboshop`

<img width="451" height="158" alt="image" src="https://github.com/user-attachments/assets/79ec2109-17e3-4f6a-9675-f8ecc9525986" />


-----------------------------------------------------------------------------------------------------------------------------



-----------------------------------------------------------------------------------------------------------------------------

frontend-Argocd  view

<img width="955" height="395" alt="image" src="https://github.com/user-attachments/assets/3b4d5b6d-96b5-46db-9ba5-557a785e23fe" />

# 🚀 RoboShop — Frontend Argo CD Application

[![Argo CD](https://img.shields.io/badge/Argo%20CD-GitOps-orange?logo=argo)](https://argo-cd.readthedocs.io/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Deployment-blue?logo=kubernetes)](https://kubernetes.io/)
[![Helm](https://img.shields.io/badge/Helm-Chart-0F1689?logo=helm)](https://helm.sh/)
[![GitHub](https://img.shields.io/badge/GitHub-Repository-black?logo=github)](https://github.com/iam-vanimina/frontend-argocd)

## 📦 Project

**RoboShop Frontend** is deployed to Kubernetes using **Argo CD + Helm** following a GitOps approach.

### 🔄 GitOps Flow

```text
👨‍💻 Developer
      │
      ▼
🐙 GitHub Repository
      │
      │  frontend-argocd
      ▼
🔴 Argo CD
      │
      │  Helm
      ▼
☸️ Kubernetes Cluster
      │
      ▼
🛍️ RoboShop Frontend
```

## ⚙️ Argo CD Application Configuration

```yaml
project: roboshop

source:
  repoURL: https://github.com/iam-vanimina/frontend-argocd.git
  path: .
  targetRevision: main

  helm:
    valueFiles:
      - values.yaml
    releaseName: frontend

destination:
  server: https://kubernetes.default.svc
  namespace: roboshop

syncPolicy:
  automated:
    prune: true
    selfHeal: true

  syncOptions:
    - CreateNamespace=true
    - ApplyOutOfSyncOnly=true
    - ServerSideApply=true
    - PruneLast=true
```

## 🔧 Configuration Details

| Configuration       | Value             |
| ------------------- | ----------------- |
| 📌 Project          | `roboshop`        |
| 🐙 Repository       | `frontend-argocd` |
| 🌿 Branch           | `main`            |
| 📁 Path             | `.`               |
| ⛵ Deployment        | Helm              |
| 📄 Values           | `values.yaml`     |
| 🚀 Release Name     | `frontend`        |
| ☸️ Namespace        | `roboshop`        |
| 🔄 Auto Sync        | Enabled           |
| 🧹 Auto Prune       | Enabled           |
| ❤️ Self Heal        | Enabled           |
| 📦 Create Namespace | Enabled           |
| ⚡ Server Side Apply | Enabled           |

## 🔄 Sync Policy

### 🤖 Automated Sync

Argo CD automatically synchronizes the Kubernetes resources whenever the Git repository changes.

```yaml
automated:
  prune: true
  selfHeal: true
```

### 🧹 Prune

```yaml
prune: true
```

Removes Kubernetes resources that no longer exist in Git.

### ❤️ Self Heal

```yaml
selfHeal: true
```

Automatically restores resources when they are manually changed in the Kubernetes cluster.

### 📦 Create Namespace

```yaml
- CreateNamespace=true
```

Automatically creates the `roboshop` namespace if it does not already exist.

### ⚡ Server Side Apply

```yaml
- ServerSideApply=true
```

Uses Kubernetes Server-Side Apply for resource management.

## 🏗️ Deployment Architecture

```text
                    ┌──────────────────────┐
                    │      GitHub 🐙        │
                    │ frontend-argocd.git   │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │       Argo CD 🔴     │
                    │                      │
                    │  GitOps Controller   │
                    └──────────┬───────────┘
                               │
                         Helm Deployment
                               │
                               ▼
                    ┌──────────────────────┐
                    │   Kubernetes ☸️      │
                    │                      │
                    │ Namespace: roboshop  │
                    │                      │
                    │  ┌────────────────┐  │
                    │  │ Frontend 🚀    │  │
                    │  └────────────────┘  │
                    └──────────────────────┘
```

## 🛠️ Technologies

* 🐙 GitHub
* 🔴 Argo CD
* ☸️ Kubernetes
* ⛵ Helm
* 🐳 Docker
* 🔄 GitOps
* 🚀 CI/CD

## 🎯 GitOps Benefits

* ✅ Automated deployments
* ✅ Continuous synchronization
* ✅ Self-healing Kubernetes resources
* ✅ Automatic pruning
* ✅ Version-controlled infrastructure
* ✅ Declarative Kubernetes configuration
* ✅ Reproducible deployments

---

### 👨‍💻 RoboShop DevOps Project

**Git Repository:** `frontend-argocd`

**Deployment:** Argo CD + Helm + Kubernetes

**Environment:** `roboshop`

--------------------------------------------------------------------------------------------------------------------------

## 🚀 Frontend Kubernetes Service

The RoboShop frontend is exposed through a Kubernetes `NodePort` service.

```yaml
apiVersion: v1
kind: Service

metadata:
  name: frontend
  namespace: roboshop
  annotations:
    argocd.argoproj.io/tracking-id: frontend:/Service:roboshop/frontend

spec:
  type: NodePort

  selector:
    app: frontend

  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080

  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
```

### 🔍 Service Configuration

| Configuration     | Value      |
| ----------------- | ---------- |
| 📦 Kind           | `Service`  |
| 🏷️ Name          | `frontend` |
| 📁 Namespace      | `roboshop` |
| 🔌 Service Port   | `80`       |
| 🎯 Target Port    | `80`       |
| 🌐 NodePort       | `30080`    |
| 🔄 Traffic Policy | `Cluster`  |
| 🔴 Managed By     | Argo CD    |

### 🌐 Traffic Flow

```text
🌍 Client
   │
   │ :30080
   ▼
☸️ Kubernetes Node
   │
   ▼
🔌 Frontend Service
   │
   │ :80
   ▼
🚀 Frontend Pod
   │
   │ :80
   ▼
🛍️ RoboShop Frontend
```

### 💡 NodePort

The frontend is exposed externally using NodePort `30080`.

```text
http://<NODE-IP>:30080
```

The Kubernetes Service receives traffic on port `80` and forwards it to the frontend application running on port `80`.

### 🔴 Argo CD Tracking

```yaml
annotations:
  argocd.argoproj.io/tracking-id: frontend:/Service:roboshop/frontend
```

This annotation allows **Argo CD** to track the `frontend` Service as part of the `frontend` application.

### ⚠️ Fields Not Normally Stored in Git

The following fields shown by `kubectl get service frontend -o yaml` are generated by Kubernetes and should generally **not** be copied into your GitOps manifest:

```yaml
creationTimestamp:
resourceVersion:
uid:
clusterIP:
clusterIPs:
ipFamilies:
ipFamilyPolicy:
```

Keep your Git manifest **declarative and minimal**; let Kubernetes generate runtime-specific values.

--------------------------------------------------------------------------------------------------------------------------
