
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


## ⚙️ Frontend NGINX Configuration

The RoboShop frontend uses an NGINX `ConfigMap` to configure:

* 🌐 Static frontend content
* 🖼️ Image handling and fallback
* 🔀 API reverse proxy
* 📦 Backend service routing
* 🗜️ Gzip compression
* 📝 Access and error logging
* 🚀 Port `8080`

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: frontend
  namespace: roboshop

  annotations:
    argocd.argoproj.io/tracking-id: frontend:/ConfigMap:roboshop/frontend

data:
  nginx.conf: |
    user www-data;
    worker_processes 4;
    pid /var/run/nginx.pid;

    events {
      worker_connections 768;
    }

    http {
      sendfile on;
      tcp_nopush on;
      tcp_nodelay on;

      keepalive_timeout 65;
      types_hash_max_size 2048;

      large_client_header_buffers 6 32k;
      client_max_body_size 100m;

      include /etc/nginx/mime.types;
      default_type application/octet-stream;

      access_log /var/log/nginx/access.log;
      error_log /var/log/nginx/error.log warn;

      gzip on;
      gzip_disable "msie6";

      include /etc/nginx/conf.d/*.conf;
      include /etc/nginx/sites-enabled/*;

      server {
        listen 8080;
        server_name localhost;

        proxy_http_version 1.1;

        location / {
          root /usr/share/nginx/html;
          index index.html index.htm;
          ssi on;
        }

        location /images/ {
          expires 5s;
          root /usr/share/nginx/html;
          try_files $uri /images/placeholder.png;
        }

        error_page 500 502 503 504 /50x.html;

        location = /50x.html {
          root /usr/share/nginx/html;
        }

        # Catalogue Service
        location /api/catalogue/ {
          proxy_pass http://catalogue:8080/;
        }

        # Cart Service
        location /api/cart/ {
          proxy_pass http://cart:8080/;
        }

        # User Service
        location /api/user/ {
          proxy_pass http://user:8080/;
        }

        # Shipping Service
        location /api/shipping/ {
          proxy_pass http://shipping:8080/;
        }

        # Payment Service
        location /api/payment/ {
          proxy_pass http://payment:8080/;
        }

        # Dispatch Service
        location /api/dispatch/ {
          proxy_pass http://dispatch:8080/;
        }
      }
    }
```

### 🔀 NGINX API Routing

The frontend NGINX acts as a **reverse proxy** between the browser and RoboShop backend microservices.

```text
                         ☸️ Kubernetes
                              │
                       ┌──────▼──────┐
                       │   Frontend  │
                       │    NGINX    │
                       │   :8080     │
                       └──────┬──────┘
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
          ▼                   ▼                   ▼
    /api/catalogue/      /api/cart/          /api/user/
          │                   │                   │
          ▼                   ▼                   ▼
      catalogue:8080       cart:8080         user:8080

          ┌───────────────────┼───────────────────┐
          │                   │                   │
          ▼                   ▼                   ▼
   /api/shipping/       /api/payment/       /api/dispatch/
          │                   │                   │
          ▼                   ▼                   ▼
     shipping:8080       payment:8080       dispatch:8080
```

### 🌐 Request Flow

```text
👤 Browser
   │
   │ HTTP Request
   ▼
🚀 Frontend Service
   │
   ▼
🔀 NGINX :8080
   │
   ├── /api/catalogue/ ──► 📦 catalogue:8080
   ├── /api/cart/      ──► 🛒 cart:8080
   ├── /api/user/      ──► 👤 user:8080
   ├── /api/shipping/  ──► 🚚 shipping:8080
   ├── /api/payment/   ──► 💳 payment:8080
   └── /api/dispatch/  ──► 📦 dispatch:8080
```

### 📋 Configuration Summary

| Feature             | Configuration     |
| ------------------- | ----------------- |
| 📦 Resource         | `ConfigMap`       |
| 🏷️ Name            | `frontend`        |
| 📁 Namespace        | `roboshop`        |
| 🌐 NGINX Port       | `8080`            |
| ⚡ Worker Processes  | `4`               |
| 🔀 Reverse Proxy    | Enabled           |
| 🗜️ Gzip            | Enabled           |
| 📏 Max Request Size | `100m`            |
| 🖼️ Image Fallback  | `placeholder.png` |
| 🔴 Argo CD Managed  | Yes               |

### 🔴 Argo CD Integration

The ConfigMap is managed by Argo CD using the tracking annotation:

```yaml
annotations:
  argocd.argoproj.io/tracking-id: frontend:/ConfigMap:roboshop/frontend
```

This allows Argo CD to associate the Kubernetes ConfigMap with the `frontend` Argo CD Application.

### ⚠️ GitOps Best Practice

Do **not** copy these fields from `kubectl get configmap frontend -o yaml` into your Git repository:

```yaml
creationTimestamp:
resourceVersion:
uid:
```

These are Kubernetes-generated runtime metadata.

Keep the Git manifest declarative and let Kubernetes generate runtime metadata automatically.
---------------------------------------------------------------------------------------------------------------------------

## 🚀 Frontend Kubernetes Deployment

The RoboShop frontend is deployed as a Kubernetes `Deployment` with **1 replica**, using a rolling-update strategy. NGINX configuration is injected from the `frontend` ConfigMap.

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: frontend
  namespace: roboshop

  labels:
    app: frontend
    project: roboshop
    tier: web

  annotations:
    argocd.argoproj.io/tracking-id: frontend:apps/Deployment:roboshop/frontend

spec:
  replicas: 1

  selector:
    matchLabels:
      app: frontend
      project: roboshop
      tier: web

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%

  template:
    metadata:
      labels:
        app: frontend
        project: roboshop
        tier: web

    spec:
      containers:
        - name: frontend
          image: vanimina/frontend:1.0.0
          imagePullPolicy: Always

          volumeMounts:
            - name: nginx-conf
              mountPath: /etc/nginx/nginx.conf
              subPath: nginx.conf
              readOnly: true

      volumes:
        - name: nginx-conf
          configMap:
            name: frontend
            items:
              - key: nginx.conf
                path: nginx.conf
            defaultMode: 420

      restartPolicy: Always
      terminationGracePeriodSeconds: 30
```

## 🏗️ Deployment Architecture

```text
                         🔴 Argo CD
                              │
                              │ GitOps
                              ▼
                    ┌───────────────────┐
                    │   Deployment      │
                    │     frontend      │
                    └─────────┬─────────┘
                              │
                              │ creates
                              ▼
                    ┌───────────────────┐
                    │   Frontend Pod    │
                    │                   │
                    │  NGINX :8080      │
                    │                   │
                    │  Image            │
                    │  vanimina/        │
                    │  frontend:1.0.0   │
                    └─────────┬─────────┘
                              │
                              │ mounts
                              ▼
                    ┌───────────────────┐
                    │    ConfigMap      │
                    │     frontend      │
                    │                   │
                    │    nginx.conf     │
                    └───────────────────┘
```

## 📋 Deployment Configuration

| Configuration        | Value                     |
| -------------------- | ------------------------- |
| 📦 Resource          | `Deployment`              |
| 🏷️ Name             | `frontend`                |
| 📁 Namespace         | `roboshop`                |
| 🏷️ Project          | `roboshop`                |
| 🎯 Tier              | `web`                     |
| 🔢 Replicas          | `1`                       |
| 🐳 Image             | `vanimina/frontend:1.0.0` |
| 🔄 Image Pull Policy | `Always`                  |
| 🚀 Strategy          | `RollingUpdate`           |
| 📈 Max Surge         | `25%`                     |
| 📉 Max Unavailable   | `25%`                     |
| ⚙️ Config            | `frontend` ConfigMap      |
| 🌐 NGINX Config      | `/etc/nginx/nginx.conf`   |
| 🔴 GitOps            | Argo CD                   |

## 🔄 Rolling Update Strategy

The Deployment uses Kubernetes `RollingUpdate`:

```yaml
strategy:
  type: RollingUpdate

  rollingUpdate:
    maxSurge: 25%
    maxUnavailable: 25%
```

This allows Kubernetes to gradually replace old frontend Pods with new Pods during an image update.

For example:

```text
Old Version
    │
    ▼
frontend:1.0.0
    │
    │ Update image
    ▼
┌─────────────────┐
│ Rolling Update  │
└────────┬────────┘
         │
         ▼
New Frontend Pod
    │
    ▼
New Version
```

## 📁 NGINX ConfigMap Mount

The `nginx.conf` file is provided by the `frontend` ConfigMap and mounted into the container:

```yaml
volumeMounts:
  - name: nginx-conf
    mountPath: /etc/nginx/nginx.conf
    subPath: nginx.conf
    readOnly: true
```

The corresponding volume is:

```yaml
volumes:
  - name: nginx-conf
    configMap:
      name: frontend
```

Therefore:

```text
ConfigMap
   │
   │ nginx.conf
   ▼
Volume
   │
   ▼
/etc/nginx/nginx.conf
   │
   ▼
NGINX
```

## 🔴 Argo CD Integration

Argo CD tracks this Deployment using:

```yaml
annotations:
  argocd.argoproj.io/tracking-id: frontend:apps/Deployment:roboshop/frontend
```

This keeps the Deployment synchronized with the desired configuration stored in Git.

## 🔍 Useful Commands

### Check Deployment

```bash
kubectl get deployment frontend -n roboshop
```

### Check Pods

```bash
kubectl get pods -n roboshop -l app=frontend
```

### Check Deployment Details

```bash
kubectl describe deployment frontend -n roboshop
```

### Check NGINX Logs

```bash
kubectl logs -n roboshop -l app=frontend
```

### Verify NGINX Configuration

```bash
kubectl exec -n roboshop deploy/frontend -- nginx -t
```

### Check Mounted Configuration

```bash
kubectl exec -n roboshop deploy/frontend -- cat /etc/nginx/nginx.conf
```

## ⚠️ GitOps Best Practice

Do not commit the following fields from the live `kubectl` output:

```yaml
creationTimestamp:
resourceVersion:
uid:
generation:
status:
```

These are generated or maintained by Kubernetes.

Your Git repository should contain the **desired state**, while Kubernetes maintains the **runtime state**.

------------------------------------------------------------------------------------------------------------------------

## 🔄 Frontend Kubernetes ReplicaSet

The `frontend` Deployment automatically creates and manages a Kubernetes `ReplicaSet`.

The ReplicaSet ensures that the desired number of frontend Pods are running.

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: frontend-86cbb79cff
  namespace: roboshop

  labels:
    app: frontend
    project: roboshop
    tier: web

spec:
  replicas: 1

  selector:
    matchLabels:
      app: frontend
      pod-template-hash: 86cbb79cff
      project: roboshop
      tier: web

  template:
    metadata:
      labels:
        app: frontend
        project: roboshop
        tier: web
        pod-template-hash: 86cbb79cff

    spec:
      containers:
        - name: frontend
          image: vanimina/frontend:1.0.0
          imagePullPolicy: Always

          volumeMounts:
            - name: nginx-conf
              mountPath: /etc/nginx/nginx.conf
              subPath: nginx.conf
              readOnly: true

      volumes:
        - name: nginx-conf
          configMap:
            name: frontend
            items:
              - key: nginx.conf
                path: nginx.conf
            defaultMode: 420

      restartPolicy: Always
      terminationGracePeriodSeconds: 30
```

### 🏗️ Deployment → ReplicaSet → Pod

```text
                    🔴 Argo CD
                         │
                         ▼
                🚀 Deployment
                  frontend
                         │
                         │ manages
                         ▼
              ┌─────────────────────┐
              │     ReplicaSet      │
              │ frontend-86cbb79cff  │
              └──────────┬──────────┘
                         │
                         │ creates
                         ▼
                 ┌───────────────┐
                 │ Frontend Pod  │
                 │               │
                 │ NGINX :8080   │
                 └───────┬───────┘
                         │
                         ▼
                  🌐 Frontend
```

### 📋 ReplicaSet Configuration

| Configuration        | Value                     |
| -------------------- | ------------------------- |
| 📦 Resource          | `ReplicaSet`              |
| 🏷️ Name             | `frontend-86cbb79cff`     |
| 📁 Namespace         | `roboshop`                |
| 🔢 Desired Replicas  | `1`                       |
| 🐳 Image             | `vanimina/frontend:1.0.0` |
| 🔄 Image Pull Policy | `Always`                  |
| 🏷️ Application      | `frontend`                |
| 🏷️ Project          | `roboshop`                |
| 🎯 Tier              | `web`                     |
| 📄 NGINX Config      | `frontend` ConfigMap      |

### 🔗 Owner Relationship

The ReplicaSet is owned by the `frontend` Deployment.

```text
Deployment
    │
    │ creates & manages
    ▼
ReplicaSet
    │
    │ creates & maintains
    ▼
Pod
```

When you update the Deployment—for example:

```yaml
image: vanimina/frontend:1.0.1
```

Kubernetes creates a **new ReplicaSet** for the new Pod template.

```text
frontend Deployment
        │
        ├── ReplicaSet: 86cbb79cff
        │       └── frontend Pod
        │
        └── ReplicaSet: NEW-HASH
                └── new frontend Pod
```

During a rolling update, Kubernetes gradually scales down the old ReplicaSet and scales up the new ReplicaSet.

### 🔍 Useful Commands

#### List ReplicaSets

```bash
kubectl get rs -n roboshop
```

#### Check Frontend ReplicaSet

```bash
kubectl get rs -n roboshop -l app=frontend
```

#### Describe ReplicaSet

```bash
kubectl describe rs frontend-86cbb79cff -n roboshop
```

#### Check Pods Created by the ReplicaSet

```bash
kubectl get pods -n roboshop \
  -l app=frontend,project=roboshop,tier=web
```

### ⚠️ GitOps / Kubernetes Best Practice

You normally **do not create this ReplicaSet YAML manually** in your Git repository.

The recommended hierarchy is:

```text
Git
 │
 ▼
Argo CD
 │
 ▼
Deployment
 │
 ▼
ReplicaSet
 │
 ▼
Pod
```

The `Deployment` is the desired-state resource you manage. Kubernetes automatically creates the ReplicaSet and manages its lifecycle.

Therefore, fields such as these from `kubectl get rs -o yaml` should not be committed:

```yaml
creationTimestamp:
generation:
resourceVersion:
uid:
ownerReferences:
status:
```

The `pod-template-hash` is also generated by the Deployment controller and should generally not be hard-coded in your Deployment manifest.


-------------------------------------------------------------------------------------------------------------------------

## 🟢 Frontend Kubernetes Pod

The `frontend` Pod is created and managed by the `frontend` ReplicaSet. The Pod runs the RoboShop frontend container using NGINX and mounts the `nginx.conf` configuration from the `frontend` ConfigMap.

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: frontend-86cbb79cff-dj56c
  namespace: roboshop

  labels:
    app: frontend
    project: roboshop
    tier: web
    pod-template-hash: 86cbb79cff

spec:
  containers:
    - name: frontend
      image: vanimina/frontend:1.0.0
      imagePullPolicy: Always

      volumeMounts:
        - name: nginx-conf
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf
          readOnly: true

  volumes:
    - name: nginx-conf
      configMap:
        name: frontend
        items:
          - key: nginx.conf
            path: nginx.conf
        defaultMode: 420

  restartPolicy: Always
  dnsPolicy: ClusterFirst
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
```

## 🏗️ Pod Architecture

```text
                 🔴 Argo CD
                     │
                     ▼
              🚀 Deployment
                 frontend
                     │
                     ▼
              🔄 ReplicaSet
            frontend-86cbb79cff
                     │
                     ▼
          ┌──────────────────────┐
          │    🟢 Frontend Pod   │
          │                      │
          │  NGINX :8080         │
          │                      │
          │  Image:              │
          │  vanimina/frontend   │
          │  :1.0.0              │
          └──────────┬───────────┘
                     │
                     │ mounts
                     ▼
              📦 ConfigMap
                 frontend
                     │
                     ▼
              nginx.conf
```

## 📋 Pod Configuration

| Configuration      | Value                       |
| ------------------ | --------------------------- |
| 📦 Resource        | `Pod`                       |
| 🏷️ Name           | `frontend-86cbb79cff-dj56c` |
| 📁 Namespace       | `roboshop`                  |
| 🐳 Image           | `vanimina/frontend:1.0.0`   |
| 🔄 Pull Policy     | `Always`                    |
| 🌐 NGINX Port      | `8080`                      |
| 🏷️ Application    | `frontend`                  |
| 🏷️ Project        | `roboshop`                  |
| 🎯 Tier            | `web`                       |
| 📄 Configuration   | `frontend` ConfigMap        |
| 🔄 Restart Policy  | `Always`                    |
| 🔐 Service Account | `default`                   |

## 🔗 Pod Ownership

This Pod is **not independently managed**.

The ownership hierarchy is:

```text
🚀 Deployment
      │
      │ manages
      ▼
🔄 ReplicaSet
      │
      │ creates
      ▼
🟢 Pod
      │
      ▼
🚀 NGINX Container
```

The ReplicaSet maintains the desired number of Pods.

If the Pod is deleted:

```bash
kubectl delete pod frontend-86cbb79cff-dj56c -n roboshop
```

the ReplicaSet will automatically create a replacement Pod.

## 📦 ConfigMap Mount

The Pod receives the NGINX configuration from the `frontend` ConfigMap.

```text
ConfigMap
    │
    │ nginx.conf
    ▼
Pod Volume
    │
    ▼
/etc/nginx/nginx.conf
    │
    ▼
NGINX
```

The configuration is mounted using:

```yaml
volumeMounts:
  - name: nginx-conf
    mountPath: /etc/nginx/nginx.conf
    subPath: nginx.conf
    readOnly: true
```

## 🌐 Pod Networking

The Pod received the Kubernetes IP:

```text
Pod IP: 10.244.1.30
```

The Pod is running on:

```text
Node: desktop-worker
```

The traffic flow is:

```text
👤 Client
    │
    ▼
🌐 NodePort :30080
    │
    ▼
🔀 Frontend Service :80
    │
    ▼
🟢 Frontend Pod :8080
    │
    ▼
🚀 NGINX
```

> **Note:** The Pod IP (`10.244.1.30`) is dynamically assigned and should not be hard-coded into your GitOps manifests.

## 🔍 Pod Status

The Pod in your output is healthy:

```text
Phase:           Running
Ready:           True
ContainersReady: True
Restart Count:   0
```

This means:

```text
🟢 Pod Scheduled
       ↓
🟢 Pod Initialized
       ↓
🟢 Container Started
       ↓
🟢 Container Ready
       ↓
🟢 Pod Running
```

## 🔎 Useful Commands

### Get Frontend Pods

```bash
kubectl get pods -n roboshop -l app=frontend
```

### Get Pod Details

```bash
kubectl describe pod frontend-86cbb79cff-dj56c -n roboshop
```

### View NGINX Logs

```bash
kubectl logs frontend-86cbb79cff-dj56c -n roboshop
```

### Test NGINX Configuration

```bash
kubectl exec -n roboshop frontend-86cbb79cff-dj56c -- nginx -t
```

### Check Mounted NGINX Configuration

```bash
kubectl exec -n roboshop frontend-86cbb79cff-dj56c -- \
  cat /etc/nginx/nginx.conf
```

## ⚠️ Do Not Commit Runtime Pod Fields

The following fields from the live Pod output are generated by Kubernetes and should normally **not** be stored in your Git repository:

```yaml
creationTimestamp:
resourceVersion:
uid:
status:
containerStatuses:
hostIP:
podIP:
startTime:
conditions:
```

Other runtime-generated values such as:

```yaml
pod-template-hash:
```

are also normally generated by the Deployment/ReplicaSet mechanism.

### 🎯 GitOps Principle

For this RoboShop application, manage the **Deployment** in Git—not the generated ReplicaSet or Pod.

```text
🐙 Git Repository
       │
       ▼
🔴 Argo CD
       │
       ▼
🚀 Deployment
       │
       ├──────────────► 🔄 ReplicaSet
       │                       │
       │                       ▼
       │                   🟢 Pod
       │
       └──────────────► 📦 ConfigMap
                              │
                              ▼
                          nginx.conf
```

This keeps your Git repository focused on the **desired state**, while Kubernetes manages the generated runtime resources.


-------------------------------------------------------------------------------------------------------------------------
