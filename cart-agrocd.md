roboshop cart argocd deployment:
-----------------------------------

Please clone below repository cart-argocd 

`https://github.com/iam-vanimina/cart-argocd.git `



`cd  /cart-argocd`

Make changes helm values as per your tags or version or image url etc ..

Make changes in application.yaml (mention your github repo url and create k8s roboshop namespace )

`kubectl apply -f application.yaml  `

In my case my github repo is 

`https://github.com/iam-vanimina/cart-argocd.git `

github repo act as the truth for the argocd.

----------------------------------------------------------------------------------------------------------------------------

# 🛒 Cart Service – Argo CD Application

The **Cart Service Argo CD Application** manages the deployment of the Cart microservice into the `roboshop` Kubernetes namespace using a **GitOps** workflow.

Argo CD continuously monitors the Cart application's Git repository and synchronizes the desired state with the Kubernetes cluster.

---

## 📄 Argo CD Application Configuration

```yaml
project: roboshop

source:
  repoURL: https://github.com/iam-vanimina/cart-argocd.git
  path: .
  targetRevision: main

  helm:
    valueFiles:
      - values.yaml
    releaseName: cart

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

---

## ⚙️ Application Configuration

| Configuration             | Value                            |
| ------------------------- | -------------------------------- |
| 🏷️ Argo CD Project       | `roboshop`                       |
| 📦 Application            | Cart Service                     |
| 📁 Git Repository         | `cart-argocd`                    |
| 🌿 Branch                 | `main`                           |
| 📂 Manifest Path          | `.`                              |
| ⛵ Deployment Tool         | Helm                             |
| 📄 Values File            | `values.yaml`                    |
| ⛵ Helm Release            | `cart`                           |
| ☸️ Kubernetes Cluster     | `https://kubernetes.default.svc` |
| 📦 Namespace              | `roboshop`                       |
| 🔄 Automated Sync         | Enabled                          |
| 🧹 Auto Prune             | Enabled                          |
| 🩹 Self Heal              | Enabled                          |
| 📌 Server-Side Apply      | Enabled                          |
| 🔢 Apply Out-of-Sync Only | Enabled                          |
| 🗑️ Prune Last            | Enabled                          |

---

## 🔄 GitOps Architecture

```text
                 📦 GitHub
                     │
                     │
                     ▼
        🛒 cart-argocd Repository
                     │
                     │ main
                     ▼
                🔴 Argo CD
                     │
                     │ Helm
                     ▼
              📄 values.yaml
                     │
                     ▼
             ⛵ Helm Release
                  cart
                     │
                     ▼
          ☸️ Kubernetes Cluster
                     │
                     ▼
             📦 roboshop Namespace
                     │
                     ▼
               🛒 Cart Service
```

---

## 🌿 Source Repository

Argo CD retrieves the Cart Service configuration from:

```text
https://github.com/iam-vanimina/cart-argocd.git
```

The configured branch is:

```yaml
targetRevision: main
```

and the Helm chart is located at:

```yaml
path: .
```

Therefore, Argo CD reads the Helm configuration from the repository root.

---

## ⛵ Helm Configuration

The application uses Helm:

```yaml
helm:
  valueFiles:
    - values.yaml

  releaseName: cart
```

The primary configuration file is:

```text
values.yaml
```

The Helm release name is:

```text
cart
```

Conceptually:

```text
🛒 cart-argocd
      │
      ├── 📄 Chart.yaml
      ├── 📄 values.yaml
      ├── 📁 templates/
      │
      ▼
   ⛵ Helm
      │
      ▼
   Release: cart
```

---

## ☸️ Kubernetes Destination

Argo CD deploys the Cart Service to:

```yaml
destination:
  server: https://kubernetes.default.svc
  namespace: roboshop
```

This means the application is deployed to the Kubernetes cluster where Argo CD itself is running.

The target namespace is:

```text
roboshop
```

---

## 🔄 Automated Synchronization

Automated synchronization is enabled:

```yaml
syncPolicy:
  automated:
    prune: true
    selfHeal: true
```

### 🧹 Automatic Pruning

```yaml
prune: true
```

If a Kubernetes resource is removed from Git but still exists in the cluster, Argo CD can automatically remove that resource.

```text
Git
 │
 │ Resource removed
 ▼
Argo CD
 │
 │ Detects difference
 ▼
☸️ Kubernetes
 │
 ▼
🗑️ Resource removed
```

### 🩹 Self-Healing

```yaml
selfHeal: true
```

If somebody manually changes a GitOps-managed resource in Kubernetes, Argo CD detects the drift and restores the configuration from Git.

```text
Git Desired State
       │
       ▼
   🔴 Argo CD
       │
       ▼
☸️ Kubernetes

Manual Change ❌
       │
       ▼
Argo CD detects drift
       │
       ▼
🔄 Reconcile
       │
       ▼
Desired State Restored ✅
```

---

## ⚙️ Sync Options

### 1. CreateNamespace

```yaml
- CreateNamespace=true
```

Allows Argo CD to create the target namespace if it does not already exist.

```text
Namespace missing
      │
      ▼
🔴 Argo CD
      │
      ▼
📦 roboshop namespace created
```

---

### 2. ApplyOutOfSyncOnly

```yaml
- ApplyOutOfSyncOnly=true
```

Argo CD applies only resources that are detected as **OutOfSync**, reducing unnecessary resource updates.

---

### 3. ServerSideApply

```yaml
- ServerSideApply=true
```

Enables Kubernetes **Server-Side Apply**, where Kubernetes manages field ownership and applies declarative configuration on the server.

This can be useful for managing complex Kubernetes resources and reducing client-side apply limitations.

---

### 4. PruneLast

```yaml
- PruneLast=true
```

Resources marked for deletion are pruned after the required updates have been applied.

Conceptually:

```text
1️⃣ Apply/Create updated resources
              │
              ▼
2️⃣ Verify synchronization
              │
              ▼
3️⃣ 🗑️ Prune obsolete resources
```

This can make synchronization safer when resources have dependencies.

---

## 🔁 Complete Deployment Flow

```text
       👨‍💻 Developer
            │
            │ Git Push
            ▼
     📦 GitHub Repository
       cart-argocd
            │
            │ main branch
            ▼
       🔴 Argo CD
            │
            │ Detect Change
            ▼
       ⛵ Helm Chart
            │
            │ values.yaml
            ▼
     📄 Kubernetes Manifests
            │
            ▼
     ☸️ Kubernetes Cluster
            │
            ▼
     📦 roboshop Namespace
            │
            ▼
       🛒 Cart Service
```

---

## 🔄 Continuous Reconciliation

The GitOps process continuously compares:

```text
┌──────────────────────┐
│    📦 Git Repository │
│   Desired State      │
└──────────┬───────────┘
           │
           │ Compare
           ▼
┌──────────────────────┐
│      🔴 Argo CD      │
│   Reconciliation     │
└──────────┬───────────┘
           │
           │ Compare
           ▼
┌──────────────────────┐
│  ☸️ Kubernetes       │
│   Actual State       │
└──────────────────────┘
```

If:

```text
Desired State = Actual State
```

Argo CD reports:

```text
✅ Synced
```

If they differ:

```text
Desired State ≠ Actual State
```

Argo CD reports:

```text
⚠️ OutOfSync
```

With automated synchronization enabled, Argo CD attempts to reconcile the cluster automatically.

---

## 🧪 Useful Argo CD Commands

### List Applications

```bash
argocd app list
```

### Get Cart Application

```bash
argocd app get cart
```

### Check Sync Status

```bash
argocd app get cart
```

Look for:

```text
Sync Status: Synced
Health Status: Healthy
```

### Manually Sync

```bash
argocd app sync cart
```

### Application History

```bash
argocd app history cart
```

### Application Resources

```bash
argocd app resources cart
```

### Application Diff

```bash
argocd app diff cart
```

---

## 🧪 Kubernetes Verification

After Argo CD synchronizes the application:

```bash
kubectl get all -n roboshop
```

Check Cart resources:

```bash
kubectl get all -n roboshop -l app=cart
```

Check Pods:

```bash
kubectl get pods -n roboshop -l app=cart
```

Check Service:

```bash
kubectl get svc -n roboshop -l app=cart
```

---

## 🏗️ Cart Service GitOps Architecture

```text
                         📦 GitHub
                            │
                            │
                  cart-argocd repository
                            │
                            ▼
                       🔴 Argo CD
                            │
                     Automated Sync
                            │
                            ▼
                      ⛵ Helm
                       release
                         cart
                            │
                            ▼
                 ☸️ Kubernetes Cluster
                            │
                            ▼
                  📦 roboshop Namespace
                            │
                ┌───────────┴───────────┐
                │                       │
                ▼                       ▼
          🚀 Deployment            🔵 Service
             cart                     cart
                │                       │
                ▼                       │
           🔄 ReplicaSet               │
                │                       │
                ▼                       │
            🟢 Pods ◄───────────────────┘
                │
                ▼
           🛒 Cart Application
```

---

## 🎯 GitOps Benefits

| Feature                   | Benefit                                    |
| ------------------------- | ------------------------------------------ |
| 📦 Git as Source of Truth | Configuration is version controlled        |
| 🔴 Argo CD                | Continuous reconciliation                  |
| ⛵ Helm                    | Templated Kubernetes deployment            |
| 🔄 Automated Sync         | Automatic deployment                       |
| 🩹 Self Heal              | Corrects manual configuration drift        |
| 🧹 Auto Prune             | Removes obsolete resources                 |
| 📜 Git History            | Tracks configuration changes               |
| ↩️ Rollback               | Previous Git/Helm versions can be restored |
| ☸️ Kubernetes             | Runs the Cart workload                     |

---

## 🏆 Cart Service Deployment Summary

```text
📦 GitHub
   │
   │ cart-argocd
   ▼
🔴 Argo CD
   │
   ├── 🔄 Automated Sync
   ├── 🩹 Self Heal
   ├── 🧹 Auto Prune
   └── ⛵ Helm
          │
          ▼
      🛒 Cart Release
          │
          ▼
   ☸️ roboshop namespace
          │
          ▼
      🛒 Cart Pods
```

### ✅ Key Points

* 🛒 Cart Service is managed through **Argo CD GitOps**.
* 📦 Source repository: `cart-argocd`.
* 🌿 Deployment branch: `main`.
* ⛵ Helm is used for Kubernetes resource generation.
* 📄 `values.yaml` provides Helm configuration.
* 📦 Helm release name is `cart`.
* ☸️ Target namespace is `roboshop`.
* 🔄 Automated synchronization is enabled.
* 🩹 `selfHeal` automatically corrects configuration drift.
* 🧹 `prune` removes resources deleted from Git.
* ⚙️ Server-Side Apply is enabled.
* 🔢 Out-of-sync resources are preferentially applied.
* 🗑️ Pruning happens after other synchronization operations.
---------------------------------------------------------------------------------------------------------------------------

# 🛒 Cart Service – ConfigMap

The **Cart Service ConfigMap** stores non-sensitive configuration values required by the Cart application.

It defines the connection details for the **Catalogue Service** and **Redis** within the Kubernetes cluster.

---

## 📄 ConfigMap YAML

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: cart
  namespace: roboshop
  annotations:
    argocd.argoproj.io/tracking-id: cart:/ConfigMap:roboshop/cart

data:
  CATALOGUE_HOST: catalogue
  CATALOGUE_PORT: "8080"
  REDIS_HOST: redis
```

---

## ⚙️ Configuration

| Variable         | Value       | Purpose                              |
| ---------------- | ----------- | ------------------------------------ |
| `CATALOGUE_HOST` | `catalogue` | Kubernetes Service name of Catalogue |
| `CATALOGUE_PORT` | `8080`      | Catalogue Service port               |
| `REDIS_HOST`     | `redis`     | Kubernetes Service name of Redis     |

### 🔗 Service Communication

```text
                    Kubernetes Cluster
                           │
             ┌─────────────┴─────────────┐
             │                           │
        🛒 Cart Pod                 📦 Dependencies
             │                           │
             ├──── CATALOGUE_HOST ─────► catalogue:8080
             │
             └──── REDIS_HOST ─────────► redis
```

Because Kubernetes provides internal DNS, the Cart application can communicate with:

```text
catalogue:8080
redis
```

without using Pod IP addresses.

---

## 🔄 ConfigMap → Cart Deployment

The Cart Deployment can load these values using:

```yaml
envFrom:
  - configMapRef:
      name: cart
```

This makes the variables available inside the Cart container:

```bash
CATALOGUE_HOST=catalogue
CATALOGUE_PORT=8080
REDIS_HOST=redis
```

---

## 🔁 GitOps Flow

```text
┌──────────────────────────────┐
│        GitHub Repository     │
│                              │
│       cart-argocd            │
│            │                 │
│       values.yaml            │
│       ConfigMap              │
└──────────────┬───────────────┘
               │
               ▼
        🔄 Argo CD
               │
        Reconciliation
               │
               ▼
      ☸️ Kubernetes
               │
               ▼
       ConfigMap/cart
               │
               ▼
          🛒 Cart Pod
               │
        ┌──────┴──────┐
        ▼             ▼
   catalogue        redis
```

---

## 🏷️ Argo CD Tracking

The annotation:

```yaml
argocd.argoproj.io/tracking-id: cart:/ConfigMap:roboshop/cart
```

allows **Argo CD** to associate this Kubernetes ConfigMap with the Cart application resource.

This helps Argo CD track and reconcile the resource during GitOps deployments.

---

## 🧹 Removed Runtime Fields

The following fields from `kubectl get configmap cart -o yaml` were removed because they are generated by Kubernetes:

```yaml
creationTimestamp
resourceVersion
uid
```

These should generally **not be committed to Git**.

---

## 🔍 Useful Kubernetes Commands

### View ConfigMap

```bash
kubectl get configmap cart -n roboshop
```

### View complete ConfigMap

```bash
kubectl get configmap cart -n roboshop -o yaml
```

### Display configured values

```bash
kubectl describe configmap cart -n roboshop
```

### Verify environment variables inside Cart Pod

```bash
kubectl exec -n roboshop <cart-pod> -- env | grep -E 'CATALOGUE|REDIS'
```

Expected:

```text
CATALOGUE_HOST=catalogue
CATALOGUE_PORT=8080
REDIS_HOST=redis
```

---

## 🔐 Configuration vs Secrets

This ConfigMap contains **non-sensitive configuration**:

```text
CATALOGUE_HOST
CATALOGUE_PORT
REDIS_HOST
```

Do **not** store passwords, tokens, API keys, or other secrets in a ConfigMap.

Use a Kubernetes `Secret` for sensitive information.

---

## 📌 Key Points

* 🛒 ConfigMap belongs to the **Cart Service**.
* 📦 Namespace: `roboshop`.
* 📚 Catalogue Service: `catalogue:8080`.
* 🔴 Redis Service: `redis`.
* 🔄 Configuration is managed through **GitOps + Argo CD**.
* 🏷️ Argo CD tracking annotation identifies the resource.
* 🧹 Kubernetes-generated metadata should not be committed to Git.
* 🔐 Sensitive values should be stored in Kubernetes Secrets.
----------------------------------------------------------------------------------------------------------------------------

# 🛒 Cart Service – Kubernetes Service

The **Cart Service Kubernetes Service** exposes the Cart application internally within the `roboshop` namespace.

It uses a **ClusterIP** service, allowing other microservices inside the Kubernetes cluster to communicate with Cart using the stable DNS name:

```text
cart:8080
```

---

## 📄 Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:
  name: cart
  namespace: roboshop
  annotations:
    argocd.argoproj.io/tracking-id: cart:/Service:roboshop/cart

spec:
  type: ClusterIP

  ports:
    - port: 8080
      targetPort: 8080
      protocol: TCP

  selector:
    app: cart
    project: roboshop
    tier: app
```

---

## ⚙️ Service Configuration

| Configuration     | Value       | Description             |
| ----------------- | ----------- | ----------------------- |
| **Service Name**  | `cart`      | Kubernetes Service name |
| **Namespace**     | `roboshop`  | Application namespace   |
| **Type**          | `ClusterIP` | Internal cluster access |
| **Port**          | `8080`      | Service port            |
| **Target Port**   | `8080`      | Cart container port     |
| **Protocol**      | `TCP`       | Network protocol        |
| **Selector**      | `app=cart`  | Selects Cart Pods       |
| **Project Label** | `roboshop`  | Project identifier      |
| **Tier Label**    | `app`       | Application tier        |

---

## 🔗 How the Service Works

```text
                    ☸️ Kubernetes Cluster
                           │
                           ▼
                  🛒 cart Service
                    ClusterIP
                    Port 8080
                           │
                ┌──────────┴──────────┐
                │                     │
                ▼                     ▼
           Cart Pod 1             Cart Pod 2
           app=cart               app=cart
                │                     │
                └──────────┬──────────┘
                           │
                    Cart Application
```

The Service uses the selector:

```yaml
selector:
  app: cart
  project: roboshop
  tier: app
```

Kubernetes automatically sends traffic to Pods matching these labels.

---

## 🌐 Kubernetes DNS

Other services inside the `roboshop` namespace can access Cart using:

```text
cart:8080
```

The fully qualified Kubernetes DNS name is:

```text
cart.roboshop.svc.cluster.local:8080
```

For example:

```text
Frontend
   │
   ▼
cart:8080
   │
   ▼
🛒 Cart Service
   │
   ▼
Cart Pod
```

This is preferable to communicating directly with a Pod IP because Pod IPs can change when Pods are recreated.

---

## 🔄 GitOps with Argo CD

The Service is managed through **Argo CD** using the tracking annotation:

```yaml
argocd.argoproj.io/tracking-id: cart:/Service:roboshop/cart
```

GitOps flow:

```text
┌──────────────────────┐
│     GitHub Repo      │
│    cart-argocd       │
└──────────┬───────────┘
           │
           │ Git Sync
           ▼
┌──────────────────────┐
│       Argo CD        │
│   Reconciliation     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│     Kubernetes       │
│                      │
│   Service: cart      │
└──────────┬───────────┘
           │
           ▼
      🛒 Cart Pods
```

---

## 🧹 Kubernetes-Generated Fields

The following fields from the live Kubernetes output should **not normally be committed to Git**:

```yaml
creationTimestamp
resourceVersion
uid
clusterIP
clusterIPs
status
```

The `clusterIP` is dynamically allocated by Kubernetes.

Therefore, the Git-managed manifest intentionally omits:

```yaml
clusterIP: 10.96.67.47
```

Kubernetes will assign the ClusterIP automatically when the Service is created.

---

## 🔍 Useful Kubernetes Commands

### Get Cart Service

```bash
kubectl get svc cart -n roboshop
```

### Get Service YAML

```bash
kubectl get svc cart -n roboshop -o yaml
```

### Describe Service

```bash
kubectl describe svc cart -n roboshop
```

### Check Service Endpoints

```bash
kubectl get endpoints cart -n roboshop
```

Or:

```bash
kubectl get endpointslices -n roboshop
```

### Check Cart Pods

```bash
kubectl get pods -n roboshop -l app=cart
```

### Test Cart Service from another Pod

```bash
kubectl exec -n roboshop <pod-name> -- curl http://cart:8080
```

---

## 📊 Service → Pod Relationship

```text
                 cart:8080
                     │
                     ▼
              ┌─────────────┐
              │ Cart Service│
              │  ClusterIP  │
              └──────┬──────┘
                     │
             Selector Matching
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
     ┌──────────┐          ┌──────────┐
     │ Cart Pod │          │ Cart Pod │
     │ app=cart │          │ app=cart │
     └──────────┘          └──────────┘
```

### 📌 Important

The Service does **not** select Pods based on the Service name.

It selects Pods using the configured labels:

```yaml
app: cart
project: roboshop
tier: app
```

Therefore, the Cart Deployment/Pods must contain matching labels.

---

## 🚀 Key Points

* 🛒 Service name: `cart`
* ☸️ Namespace: `roboshop`
* 🔒 Service type: `ClusterIP`
* 🔌 Port: `8080`
* 🎯 Target port: `8080`
* 🏷️ Selects Pods using Cart labels
* 🌐 Accessible internally using `cart:8080`
* 🔄 Managed through Argo CD and GitOps
* 🧹 ClusterIP and runtime metadata are omitted from Git
* ⚖️ Kubernetes distributes traffic across matching Cart Pods
--------------------------------------------------------------------------------------------------------------------

# 🛒 Cart Service – Kubernetes Deployment

The **Cart Deployment** manages the lifecycle of Cart application Pods in the `roboshop` namespace.

It ensures the desired number of Cart Pods are running and provides **rolling updates**, **ConfigMap-based configuration**, and integration with the Cart Kubernetes Service.

---

## 📄 Deployment YAML

```yaml id="v4g8zq"
apiVersion: apps/v1
kind: Deployment

metadata:
  name: cart
  namespace: roboshop
  labels:
    app: cart
    project: roboshop
    tier: app
  annotations:
    argocd.argoproj.io/tracking-id: cart:apps/Deployment:roboshop/cart

spec:
  replicas: 1

  selector:
    matchLabels:
      app: cart
      project: roboshop
      tier: app

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%

  template:
    metadata:
      labels:
        app: cart
        project: roboshop
        tier: app

    spec:
      containers:
        - name: cart
          image: vanimina/cart:1.0.0
          imagePullPolicy: Always

          envFrom:
            - configMapRef:
                name: cart

          resources: {}

      restartPolicy: Always
      dnsPolicy: ClusterFirst
      terminationGracePeriodSeconds: 30

  progressDeadlineSeconds: 600
  revisionHistoryLimit: 10
```

---

## ⚙️ Deployment Configuration

| Configuration        | Value                 | Purpose                                   |
| -------------------- | --------------------- | ----------------------------------------- |
| **Deployment Name**  | `cart`                | Cart application Deployment               |
| **Namespace**        | `roboshop`            | Application namespace                     |
| **Replicas**         | `1`                   | Desired Cart Pod count                    |
| **Container**        | `cart`                | Container name                            |
| **Image**            | `vanimina/cart:1.0.0` | Cart application image                    |
| **Pull Policy**      | `Always`              | Always checks the registry for the image  |
| **Service Port**     | `8080`                | Cart application port                     |
| **ConfigMap**        | `cart`                | Provides application configuration        |
| **Strategy**         | `RollingUpdate`       | Zero/minimal-downtime deployment strategy |
| **Max Surge**        | `25%`                 | Temporary extra Pods during update        |
| **Max Unavailable**  | `25%`                 | Maximum unavailable Pods during update    |
| **Revision History** | `10`                  | Number of old ReplicaSets retained        |

---

# 🔗 Deployment → ReplicaSet → Pod

A Kubernetes Deployment does not directly create Pods.

The hierarchy is:

```text
                    🛒 Cart Deployment
                           │
                           │ manages
                           ▼
                  ┌─────────────────┐
                  │    ReplicaSet    │
                  │   cart-xxxxxxx   │
                  └────────┬────────┘
                           │
                       creates
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
        ┌───────────┐             ┌───────────┐
        │ Cart Pod  │             │ Cart Pod  │
        │ app=cart  │             │ app=cart  │
        └───────────┘             └───────────┘
```

With:

```yaml
replicas: 1
```

Kubernetes attempts to maintain **one running Cart Pod**.

---

# 🏷️ Label & Selector Configuration

The Deployment uses these labels:

```yaml
labels:
  app: cart
  project: roboshop
  tier: app
```

The Deployment selector must match the Pod template labels:

```yaml
selector:
  matchLabels:
    app: cart
    project: roboshop
    tier: app
```

And the Pod template contains:

```yaml
template:
  metadata:
    labels:
      app: cart
      project: roboshop
      tier: app
```

This relationship is important:

```text
Deployment Selector
        │
        │ matches
        ▼
Pod Labels
        │
        ▼
Cart Pods
```

---

# 🔄 ConfigMap Integration

The Cart container loads environment variables from the `cart` ConfigMap:

```yaml
envFrom:
  - configMapRef:
      name: cart
```

The ConfigMap contains:

```text
CATALOGUE_HOST=catalogue
CATALOGUE_PORT=8080
REDIS_HOST=redis
```

Therefore:

```text
                 🛒 Cart Pod
                     │
                     │ envFrom
                     ▼
              ConfigMap: cart
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
    CATALOGUE_HOST  CATALOGUE_PORT  REDIS_HOST
       catalogue        8080          redis
```

---

# 🚀 Container Image

The Deployment uses:

```yaml
image: vanimina/cart:1.0.0
```

This identifies:

```text
Registry/Repository : vanimina/cart
Version/Tag         : 1.0.0
```

With:

```yaml
imagePullPolicy: Always
```

Kubernetes checks the container registry whenever a new container is started.

> 💡 For production environments, using immutable image tags such as versioned tags or image digests is preferable to mutable tags such as `latest`.

---

# 🔄 Rolling Update Strategy

The Deployment uses:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 25%
    maxUnavailable: 25%
```

During an application update:

```text
Old Version
    │
    ▼
┌─────────────┐
│ Cart v1.0.0 │
└─────────────┘
       │
       │ Update image
       ▼
┌─────────────────┐
│ Create new Pod  │
│ Cart v1.1.0     │
└─────────────────┘
       │
       ▼
Old Pod terminated
       │
       ▼
New version running
```

This allows Kubernetes to gradually replace the old Pods with the new version.

---

# 🌐 Cart Service Integration

The Cart Deployment works together with the Cart Kubernetes Service.

```text
             Other Microservices
                    │
                    │ cart:8080
                    ▼
             ┌──────────────┐
             │ Cart Service │
             │  ClusterIP   │
             └───────┬──────┘
                     │
                Selector
                     │
                     ▼
             ┌──────────────┐
             │  Cart Pod    │
             │   :8080      │
             └──────────────┘
```

The Cart Service selects Pods using:

```yaml
selector:
  app: cart
  project: roboshop
  tier: app
```

The Deployment creates Pods with the same labels, allowing the Service to route traffic to them.

---

# 🔄 GitOps with Argo CD

The Deployment is managed by **Argo CD**.

The tracking annotation is:

```yaml
argocd.argoproj.io/tracking-id: cart:apps/Deployment:roboshop/cart
```

GitOps flow:

```text
┌───────────────────────────┐
│       GitHub Repository   │
│                           │
│      cart-argocd          │
│                           │
│  Helm + Kubernetes YAML   │
└─────────────┬─────────────┘
              │
              │ Git
              ▼
┌───────────────────────────┐
│          Argo CD          │
│                           │
│   Continuous Reconcile    │
└─────────────┬─────────────┘
              │
              │ Apply
              ▼
┌───────────────────────────┐
│       Kubernetes          │
│                           │
│    Deployment: cart       │
└─────────────┬─────────────┘
              │
              ▼
         ReplicaSet
              │
              ▼
          Cart Pod
```

If the Deployment configuration changes in Git, Argo CD can synchronize the desired state to Kubernetes.

---

# 🧠 Why Deployment Instead of Pod?

A standalone Pod is not ideal for application management.

A Deployment provides:

* 🔄 Automatic Pod replacement
* 📈 Scaling
* 🚀 Rolling updates
* ↩️ Rollback support
* 🩺 Desired-state management
* 🛡️ Self-healing through ReplicaSets
* 🔗 Integration with Services and HPA

For the Cart application:

```text
Deployment
    │
    ├── ReplicaSet
    │      └── Cart Pod
    │
    ├── HPA
    │
    └── Service
           └── Cart Pod
```

---

# 🧹 Runtime Fields Removed

The original output came from a live Kubernetes object, so it contained generated fields.

These should normally **not be committed to Git**:

```text
creationTimestamp
generation
resourceVersion
uid
deployment.kubernetes.io/revision
status
conditions
availableReplicas
readyReplicas
updatedReplicas
observedGeneration
```

These values are generated and maintained by Kubernetes.

The Git-managed manifest should describe the **desired state**, not the current runtime state.

---

# 🔍 Useful Kubernetes Commands

### View Deployment

```bash
kubectl get deployment cart -n roboshop
```

### View Deployment YAML

```bash
kubectl get deployment cart -n roboshop -o yaml
```

### Describe Deployment

```bash
kubectl describe deployment cart -n roboshop
```

### Check ReplicaSets

```bash
kubectl get rs -n roboshop -l app=cart
```

### Check Cart Pods

```bash
kubectl get pods -n roboshop -l app=cart
```

### Watch Deployment

```bash
kubectl rollout status deployment/cart -n roboshop
```

### Check Rollout History

```bash
kubectl rollout history deployment/cart -n roboshop
```

### Rollback

```bash
kubectl rollout undo deployment/cart -n roboshop
```

### View Cart Logs

```bash
kubectl logs -n roboshop -l app=cart
```

---

# 🛡️ Production Recommendation – Resource Requests & Limits

Currently the Deployment contains:

```yaml
resources: {}
```

This means the Cart container has no CPU or memory requests/limits configured.

For production, consider defining them:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

This is particularly important because your Cart application also uses an **HPA**. CPU-based HPA calculations depend on CPU resource requests.

---

# 📌 Key Points

* 🛒 Deployment name: `cart`
* ☸️ Namespace: `roboshop`
* 📦 Image: `vanimina/cart:1.0.0`
* 🔢 Desired replicas: `1`
* 🔄 Strategy: `RollingUpdate`
* ⚙️ Configuration: `ConfigMap/cart`
* 🌐 Service: `cart:8080`
* 🏷️ Labels: `app=cart`, `project=roboshop`, `tier=app`
* 🔄 Managed by Argo CD
* 🛡️ ReplicaSet provides Pod self-healing
* 📈 HPA can dynamically adjust replicas
* 🧹 Runtime-generated fields are excluded from Git
* ⚠️ CPU/memory requests and limits should be added for production
---------------------------------------------------------------------------------------------------------------------------

# 🛒 Cart Service – Horizontal Pod Autoscaler (HPA)

The **Horizontal Pod Autoscaler (HPA)** automatically adjusts the number of Cart Pods based on CPU utilization.

For the Cart Service, the HPA maintains between **1 and 10 replicas** and attempts to keep average CPU utilization around **20%**.

---

## 📄 HPA YAML

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler

metadata:
  name: cart
  namespace: roboshop
  annotations:
    argocd.argoproj.io/tracking-id: cart:autoscaling/HorizontalPodAutoscaler:roboshop/cart

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: cart

  minReplicas: 1
  maxReplicas: 10

  targetCPUUtilizationPercentage: 20
```

---

## ⚙️ HPA Configuration

| Configuration        |             Value | Description                    |
| -------------------- | ----------------: | ------------------------------ |
| **HPA Name**         |            `cart` | HPA resource name              |
| **Namespace**        |        `roboshop` | Application namespace          |
| **Target**           | `Deployment/cart` | Deployment controlled by HPA   |
| **Minimum Replicas** |               `1` | Minimum Cart Pods              |
| **Maximum Replicas** |              `10` | Maximum Cart Pods              |
| **CPU Target**       |             `20%` | Target average CPU utilization |
| **API Version**      |  `autoscaling/v1` | HPA API version                |

---

# 🔄 How HPA Works

The HPA continuously monitors CPU utilization and adjusts the number of replicas in the Cart Deployment.

```text
                  📊 CPU Metrics
                       │
                       ▼
              ┌─────────────────┐
              │       HPA       │
              │      cart       │
              │                 │
              │ Target CPU: 20% │
              └────────┬────────┘
                       │
                Scale Deployment
                       │
                       ▼
              ┌─────────────────┐
              │  Deployment     │
              │      cart       │
              └────────┬────────┘
                       │
              ┌────────┴────────┐
              ▼                 ▼
          Cart Pod 1        Cart Pod 2
```

### 📈 When CPU increases

If CPU utilization goes significantly above the target:

```text
CPU ↑
 │
 ▼
HPA detects high utilization
 │
 ▼
Increase replicas
 │
 ▼
1 Pod → 2 Pods → 3 Pods → ...
```

### 📉 When CPU decreases

When utilization falls:

```text
CPU ↓
 │
 ▼
HPA detects lower utilization
 │
 ▼
Decrease replicas
 │
 ▼
Multiple Pods → fewer Pods
```

The HPA will not scale below:

```text
minReplicas = 1
```

and will not scale above:

```text
maxReplicas = 10
```

---

# 🎯 CPU Target – 20%

The configuration:

```yaml
targetCPUUtilizationPercentage: 20
```

means the HPA attempts to maintain average CPU utilization at approximately **20% of the CPU requests configured for the Pods**.

For example, if the Cart Deployment has:

```yaml
resources:
  requests:
    cpu: "100m"
```

then a 20% CPU utilization target corresponds to approximately:

```text
20% of 100m = 20m CPU
```

> ⚠️ CPU requests are important for CPU-based HPA calculations. Your current Cart Deployment has `resources: {}`, so adding appropriate CPU requests is recommended.

---

# 🔗 HPA → Deployment

The HPA targets:

```yaml
scaleTargetRef:
  apiVersion: apps/v1
  kind: Deployment
  name: cart
```

Therefore:

```text
┌─────────────────────┐
│  HPA: cart          │
│                     │
│ Min: 1              │
│ Max: 10             │
│ CPU: 20%            │
└──────────┬──────────┘
           │
           │ scales
           ▼
┌─────────────────────┐
│ Deployment: cart    │
│                     │
│ replicas: 1         │
└──────────┬──────────┘
           │
           ▼
      ReplicaSet
           │
           ▼
        Cart Pods
```

The HPA changes the Deployment's desired replica count. The Deployment then manages the corresponding ReplicaSet and Pods.

---

# 🚨 Current HPA Issue

Your live HPA output contains:

```text
FailedGetResourceMetric
```

and:

```text
unable to fetch metrics from resource metrics API:
the server could not find the requested resource
(get pods.metrics.k8s.io)
```

This indicates that the Kubernetes **Resource Metrics API is unavailable** in your cluster.

Therefore, the HPA currently cannot retrieve CPU metrics.

Your current state is effectively:

```text
Cart HPA
   │
   ▼
Request CPU Metrics
   │
   ▼
metrics.k8s.io
   │
   ❌ Not Available
   │
   ▼
Cannot calculate CPU utilization
```

---

# 🔍 Check Metrics Server

Check whether Metrics Server is installed:

```bash
kubectl get deployment metrics-server -n kube-system
```

Check Metrics API:

```bash
kubectl get apiservice v1beta1.metrics.k8s.io
```

Check whether the API responds:

```bash
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes"
```

Check Pod metrics:

```bash
kubectl top pods -n roboshop
```

Check node metrics:

```bash
kubectl top nodes
```

If `kubectl top` does not work, the Metrics API is likely not functioning correctly.

---

# 🛠️ Resource Requests for HPA

Your Cart Deployment currently contains:

```yaml
resources: {}
```

For a production deployment, consider:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

Then the HPA can use the CPU request as the basis for calculating utilization.

---

# 🔄 GitOps + Argo CD

The HPA is managed by Argo CD using:

```yaml
argocd.argoproj.io/tracking-id: cart:autoscaling/HorizontalPodAutoscaler:roboshop/cart
```

GitOps flow:

```text
┌──────────────────────────┐
│      GitHub Repository   │
│                          │
│       cart-argocd        │
│                          │
│       HPA YAML           │
└────────────┬─────────────┘
             │
             ▼
        🔄 Argo CD
             │
             ▼
     Kubernetes Cluster
             │
             ▼
       ┌───────────┐
       │ Cart HPA  │
       └─────┬─────┘
             │
             ▼
      Deployment/cart
             │
             ▼
          Cart Pods
```

---

# 🧹 Runtime Fields Removed

The original `kubectl` output contains runtime information that should not normally be committed to Git.

Removed fields include:

```text
creationTimestamp
resourceVersion
uid
status
currentReplicas
desiredReplicas
autoscaling.alpha.kubernetes.io/conditions
autoscaling.alpha.kubernetes.io/current-metrics
```

These values are maintained by Kubernetes/HPA at runtime.

The Git-managed manifest should contain the **desired configuration**, not the current runtime status.

---

# 🔍 Useful HPA Commands

### View HPA

```bash
kubectl get hpa cart -n roboshop
```

### Detailed HPA information

```bash
kubectl describe hpa cart -n roboshop
```

### Watch HPA

```bash
kubectl get hpa cart -n roboshop -w
```

### Check Cart Deployment

```bash
kubectl get deployment cart -n roboshop
```

### Check Cart Pods

```bash
kubectl get pods -n roboshop -l app=cart
```

### Check CPU metrics

```bash
kubectl top pods -n roboshop
```

---

# 📊 Complete Cart Scaling Architecture

```text
                         📊 CPU Usage
                              │
                              ▼
                    ┌──────────────────┐
                    │    Metrics API   │
                    │                  │
                    │ metrics.k8s.io   │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │    Cart HPA      │
                    │                  │
                    │ Min: 1            │
                    │ Max: 10           │
                    │ CPU: 20%          │
                    └────────┬─────────┘
                             │
                        Scale Target
                             │
                             ▼
                    ┌──────────────────┐
                    │ Cart Deployment  │
                    │                  │
                    │ replicas: 1       │
                    └────────┬─────────┘
                             │
                             ▼
                        ReplicaSet
                             │
                  ┌──────────┴──────────┐
                  ▼                     ▼
             Cart Pod 1            Cart Pod 2
                  │                     │
                  └──────────┬──────────┘
                             │
                             ▼
                       🛒 Cart Service
                          cart:8080
```

---

# 📌 Key Points

* 🛒 HPA name: `cart`
* ☸️ Namespace: `roboshop`
* 🎯 Target: `Deployment/cart`
* 📉 Minimum replicas: `1`
* 📈 Maximum replicas: `10`
* 🧮 CPU target: `20%`
* 🔄 Automatically adjusts Cart Pod count
* 📊 Requires working Kubernetes Resource Metrics API
* ⚠️ Your current cluster reports `pods.metrics.k8s.io` unavailable
* 🛠️ `metrics-server` should be checked/fixed
* 💻 CPU requests should be configured for reliable CPU-based HPA behavior
* 🔄 HPA configuration is managed through Argo CD/GitOps
------------------------------------------------------------------------------------------------------------------------
# 🛒 Cart Service – ReplicaSet

A **ReplicaSet** ensures that the desired number of Cart application Pods are running.

In this project, the ReplicaSet is **automatically created and managed by the `cart` Deployment**.

```text
Deployment
    │
    ▼
ReplicaSet
    │
    ▼
Cart Pod
```

---

## 📄 ReplicaSet YAML

> ⚠️ **Important:** This is a cleaned representation of the live ReplicaSet. The `pod-template-hash` value is generated by Kubernetes and should not normally be hard-coded in your Git repository.

```yaml id="3m5q8v"
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: cart-fbf947fd7
  namespace: roboshop
  labels:
    app: cart
    project: roboshop
    tier: app
  annotations:
    argocd.argoproj.io/tracking-id: cart:apps/Deployment:roboshop/cart

spec:
  replicas: 1

  selector:
    matchLabels:
      app: cart
      project: roboshop
      tier: app
      pod-template-hash: fbf947fd7

  template:
    metadata:
      labels:
        app: cart
        project: roboshop
        tier: app
        pod-template-hash: fbf947fd7

    spec:
      containers:
        - name: cart
          image: vanimina/cart:1.0.0
          imagePullPolicy: Always

          envFrom:
            - configMapRef:
                name: cart

          resources: {}

      restartPolicy: Always
      dnsPolicy: ClusterFirst
      terminationGracePeriodSeconds: 30
```

---

# 🏗️ Deployment → ReplicaSet → Pod

The Cart application follows the standard Kubernetes workload hierarchy:

```text id="vuw8op"
                 🛒 Deployment
                    cart
                     │
                     │ manages
                     ▼
              ┌──────────────┐
              │  ReplicaSet   │
              │ cart-xxxxxxx  │
              └───────┬──────┘
                      │
                      │ creates
                      ▼
               ┌─────────────┐
               │   Cart Pod  │
               │   app=cart  │
               └─────────────┘
```

The Deployment specifies:

```yaml id="3p4j1r"
replicas: 1
```

The ReplicaSet then ensures that **one matching Cart Pod** exists.

---

# 🔄 Self-Healing

One of the primary responsibilities of a ReplicaSet is maintaining the desired number of Pods.

For example:

```text id="j1u7kc"
Desired:
1 Cart Pod

       │
       ▼

Cart Pod crashes
       │
       ▼
ReplicaSet detects
missing Pod
       │
       ▼
New Cart Pod created
       │
       ▼
Desired state restored
```

Therefore:

```text
replicas = 1
```

means Kubernetes continuously attempts to maintain one matching Pod.

---

# 🏷️ Label Selectors

The ReplicaSet uses labels to identify the Pods it manages.

```yaml id="m7r4q2"
selector:
  matchLabels:
    app: cart
    project: roboshop
    tier: app
```

The Pod template contains matching labels:

```yaml id="2u8c1n"
labels:
  app: cart
  project: roboshop
  tier: app
```

The relationship is:

```text id="e3f9k6"
ReplicaSet Selector
        │
        │ matches
        ▼
Pod Labels
        │
        ▼
Cart Pod
```

This allows the ReplicaSet to determine whether the required Cart Pods exist.

---

# 🔢 Pod Template Hash

The live ReplicaSet contains:

```text id="f6z2p8"
pod-template-hash: fbf947fd7
```

This value is generated by the Deployment controller.

It distinguishes different versions of a Pod template.

For example:

```text id="y8x2c1"
Deployment: cart

       │
       ├── ReplicaSet cart-fbf947fd7
       │       └── Cart Pods
       │
       └── New version
               │
               ▼
          ReplicaSet cart-xxxxxxxx
               └── New Cart Pods
```

During a rolling update, Kubernetes can temporarily maintain old and new ReplicaSets.

> ⚠️ **Do not manually choose or hard-code `pod-template-hash` in a Deployment manifest.** Kubernetes generates it automatically.

---

# 🔄 Rolling Updates

The Cart Deployment uses:

```yaml id="a4h8q0"
strategy:
  type: RollingUpdate
```

When the Cart image changes:

```text id="b9k5r3"
cart:1.0.0
     │
     │ Image update
     ▼
cart:1.1.0
     │
     ▼
New Pod Template
     │
     ▼
New ReplicaSet
     │
     ▼
New Cart Pod
```

The old ReplicaSet can remain temporarily while Kubernetes performs the rollout.

The Deployment ultimately controls which ReplicaSet should contain the desired number of replicas.

---

# ⚙️ Cart Container Configuration

The ReplicaSet's Pod template uses:

```yaml id="w1n6p4"
containers:
  - name: cart
    image: vanimina/cart:1.0.0
    imagePullPolicy: Always
```

The Cart configuration comes from:

```yaml id="r6x3m9"
envFrom:
  - configMapRef:
      name: cart
```

The `cart` ConfigMap provides:

```text id="q5b8s2"
CATALOGUE_HOST=catalogue
CATALOGUE_PORT=8080
REDIS_HOST=redis
```

So the ReplicaSet creates Pods with the required Cart application configuration.

---

# 🌐 ReplicaSet + Service

The ReplicaSet creates the Cart Pod, while the Cart Service provides stable network access.

```text id="u3c7n5"
             cart:8080
                 │
                 ▼
          ┌─────────────┐
          │ Cart Service│
          │  ClusterIP  │
          └──────┬──────┘
                 │
              Selector
                 │
                 ▼
          ┌─────────────┐
          │  Cart Pod   │
          │ app=cart    │
          └─────────────┘
```

The Service selects the Pod using:

```yaml id="r1v6k3"
app: cart
project: roboshop
tier: app
```

The ReplicaSet ensures that the matching Pod exists.

---

# 🔄 Argo CD Relationship

Your ReplicaSet contains this tracking annotation:

```yaml id="p8k2m4"
argocd.argoproj.io/tracking-id: cart:apps/Deployment:roboshop/cart
```

Notice that the tracking ID references:

```text
Deployment/cart
```

rather than treating the ReplicaSet as an independently managed GitOps application resource.

This reflects the ownership chain:

```text id="c4y7w1"
Git
 │
 ▼
Argo CD
 │
 ▼
Deployment/cart
 │
 ▼
ReplicaSet
 │
 ▼
Cart Pod
```

---

# 🧹 Generated Fields Removed

The live Kubernetes output contains several runtime-generated fields.

These should normally **not be committed to Git**:

```text id="k2f8r6"
creationTimestamp
generation
resourceVersion
uid
ownerReferences
deployment.kubernetes.io/desired-replicas
deployment.kubernetes.io/max-replicas
deployment.kubernetes.io/revision
status
availableReplicas
fullyLabeledReplicas
readyReplicas
observedGeneration
terminatingReplicas
```

The `ownerReferences` field is especially important because it shows that:

```text
ReplicaSet → owned by Deployment/cart
```

Kubernetes automatically creates and manages this relationship.

---

# 🚫 Should You Create This ReplicaSet YAML Manually?

**Normally, no.**

Your GitOps repository should contain the Deployment:

```text
Deployment/cart
```

and Kubernetes will automatically create:

```text
ReplicaSet/cart-xxxxxxxx
```

which then creates:

```text
Pod/cart-xxxxxxxx-xxxxx
```

Recommended GitOps structure:

```text id="h4j9s7"
cart-argocd/
│
├── Chart.yaml
├── values.yaml
└── templates/
    ├── configmap.yaml
    ├── service.yaml
    ├── deployment.yaml
    └── hpa.yaml
```

Not:

```text id="n7q3w5"
❌ deployment.yaml
❌ replicaset.yaml
❌ pod.yaml
```

The Deployment is the desired-state resource; ReplicaSets and Pods are normally generated by Kubernetes.

---

# 🔍 Useful Commands

### View ReplicaSet

```bash id="x2m8c4"
kubectl get rs -n roboshop -l app=cart
```

### Describe ReplicaSet

```bash id="f7q1k3"
kubectl describe rs cart-fbf947fd7 -n roboshop
```

### View ReplicaSet YAML

```bash id="v5n2d8"
kubectl get rs cart-fbf947fd7 -n roboshop -o yaml
```

### Check Pods managed by Cart

```bash id="m9r4t6"
kubectl get pods -n roboshop -l app=cart
```

### View Deployment

```bash id="z3k7p1"
kubectl get deployment cart -n roboshop
```

### Check Deployment → ReplicaSet relationship

```bash id="s6w2h9"
kubectl describe deployment cart -n roboshop
```

---

# 📊 Complete Cart Workload Architecture

```text id="e8p3v6"
                  GitHub
                     │
                     ▼
                  Argo CD
                     │
                     ▼
             ┌───────────────┐
             │ Deployment    │
             │     cart      │
             └───────┬───────┘
                     │
                 manages
                     │
                     ▼
             ┌───────────────┐
             │  ReplicaSet   │
             │ cart-xxxxxxx  │
             └───────┬───────┘
                     │
                  creates
                     │
                     ▼
             ┌───────────────┐
             │    Cart Pod   │
             │   :8080       │
             └───────┬───────┘
                     │
                     ▲
                     │
             ┌───────┴───────┐
             │ Cart Service  │
             │   cart:8080   │
             └───────────────┘

             ┌───────────────┐
             │   Cart HPA    │
             │   1 → 10 Pods │
             └───────┬───────┘
                     │
                     ▼
                Deployment
```

---

# 📌 Key Points

* 🛒 ReplicaSet: `cart-fbf947fd7`
* ☸️ Namespace: `roboshop`
* 🔢 Desired replicas: `1`
* 🏗️ Created and managed by `Deployment/cart`
* 🩺 Provides Pod self-healing
* 🏷️ Uses Cart labels and selectors
* 🔢 `pod-template-hash` is generated automatically
* 🔄 Supports Deployment rolling updates
* ⚙️ Uses `ConfigMap/cart`
* 🌐 Cart Service routes traffic to matching Pods
* 📈 Cart HPA can change the Deployment replica count
* 🔄 Argo CD manages the desired Deployment state
* 🚫 ReplicaSet normally should not be maintained as a separate GitOps manifest
----------------------------------------------------------------------------------------------------------------------

# 🛒 Cart Service – Kubernetes Pod

The **Cart Pod** is the actual runtime unit that runs the Cart application container.

In this architecture, the Pod is created and managed by the **Cart ReplicaSet**, which itself is managed by the **Cart Deployment**.

```text
Deployment
    │
    ▼
ReplicaSet
    │
    ▼
Cart Pod
    │
    ▼
Cart Container
```

---

## 📄 Pod YAML

> ⚠️ This is a **cleaned representation** of the live Pod. The actual Pod name, Pod IP, node, container ID, `pod-template-hash`, and status information are generated by Kubernetes.

```yaml id="x7n4kp"
apiVersion: v1
kind: Pod

metadata:
  name: cart
  namespace: roboshop
  labels:
    app: cart
    project: roboshop
    tier: app

spec:
  containers:
    - name: cart
      image: vanimina/cart:1.0.0
      imagePullPolicy: Always

      envFrom:
        - configMapRef:
            name: cart

      resources: {}

  restartPolicy: Always
  dnsPolicy: ClusterFirst
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
```

---

# 🏗️ Cart Workload Hierarchy

The Cart application follows the standard Kubernetes workload hierarchy:

```text
                         🛒 Cart
                           │
                           ▼
                    ┌─────────────┐
                    │ Deployment  │
                    │    cart     │
                    └──────┬──────┘
                           │
                       manages
                           │
                           ▼
                    ┌─────────────┐
                    │ ReplicaSet  │
                    │ cart-xxxxxxx│
                    └──────┬──────┘
                           │
                       creates
                           │
                           ▼
                    ┌─────────────┐
                    │  Cart Pod   │
                    │             │
                    │ cart:8080   │
                    └──────┬──────┘
                           │
                           ▼
                    Cart Container
```

---

# ⚙️ Cart Container

The Pod runs:

```yaml id="a8q5tz"
containers:
  - name: cart
    image: vanimina/cart:1.0.0
    imagePullPolicy: Always
```

The container image is:

```text id="v6k3nm"
Repository : vanimina/cart
Version   : 1.0.0
```

The application runs inside the Cart container and communicates with other services through Kubernetes networking.

---

# 🔗 ConfigMap Integration

The Cart Pod receives its configuration from:

```yaml id="q1w8hc"
envFrom:
  - configMapRef:
      name: cart
```

The `cart` ConfigMap contains:

```text id="m9z2rv"
CATALOGUE_HOST=catalogue
CATALOGUE_PORT=8080
REDIS_HOST=redis
```

Therefore:

```text id="r5c8nx"
                🛒 Cart Pod
                    │
                    │ envFrom
                    ▼
              ConfigMap/cart
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
   catalogue      8080        redis
```

This allows the Cart application to discover its dependencies using Kubernetes Service names.

---

# 🌐 Cart Pod Networking

Your live Pod received:

```text id="h4v7cs"
Pod IP : 10.244.2.11
```

However, **Pod IPs are temporary**.

If the Pod is deleted and recreated, Kubernetes may assign a different IP.

Therefore, applications should normally access Cart through the Kubernetes Service:

```text id="k7m2px"
cart:8080
```

instead of:

```text id="n6q1dw"
10.244.2.11:8080
```

The networking flow is:

```text id="b3t9fj"
Other Service
     │
     ▼
cart:8080
     │
     ▼
Cart Service
     │
     ▼
Cart Pod
     │
     ▼
Cart Container
```

---

# 🏷️ Pod Labels

The Cart Pod uses:

```yaml id="s5n8rq"
labels:
  app: cart
  project: roboshop
  tier: app
```

These labels are important because the Cart Service uses them to identify the correct Pods.

```text id="c7y2mv"
Cart Service
     │
     │ selector
     ▼
app=cart
project=roboshop
tier=app
     │
     ▼
Cart Pod
```

---

# 🔢 Pod Template Hash

The live Pod contains:

```text id="q4x8mv"
pod-template-hash: fbf947fd7
```

This value comes from the Deployment/ReplicaSet mechanism and identifies the specific Pod template version.

The actual Pod name was:

```text id="h8k2ps"
cart-fbf947fd7-hnbg6
```

These names are generated automatically.

You should **not manually create Pods with these generated names** when the application is managed by a Deployment.

---

# 🖥️ Node Scheduling

The live Cart Pod was scheduled onto:

```text id="w6r3kn"
desktop-worker2
```

This is determined by the Kubernetes scheduler.

The simplified Pod manifest does not specify:

```yaml
nodeName: desktop-worker2
```

because hard-coding a node name would make the workload unnecessarily tied to one specific node.

Kubernetes should normally decide where the Pod runs.

```text id="q9m4vx"
              Kubernetes Scheduler
                       │
                       ▼
              ┌─────────────────┐
              │ desktop-worker2 │
              └────────┬────────┘
                       │
                       ▼
                   Cart Pod
```

---

# 🔐 Service Account

The Pod uses:

```yaml id="x5j8rm"
serviceAccountName: default
```

Kubernetes also automatically provides the ServiceAccount-related projected volume in the live Pod.

Your live Pod contained:

```text id="m3v7kp"
/var/run/secrets/kubernetes.io/serviceaccount
```

This is runtime-generated and does not need to be manually defined for this basic application.

> 💡 For production workloads, consider using a dedicated ServiceAccount with only the permissions the application actually needs.

---

# 🔄 Pod Lifecycle

The Cart Pod is managed by the ReplicaSet.

```text id="v8q2mc"
Pod Running
     │
     │ Crash / deletion
     ▼
Pod disappears
     │
     ▼
ReplicaSet detects
missing replica
     │
     ▼
New Cart Pod
     │
     ▼
Pod Running
```

This provides basic workload self-healing.

---

# 🩺 Current Pod Status

Your live Pod showed:

```text id="p6w3zn"
Phase          : Running
Ready          : True
Container      : Ready
Restart Count  : 0
QoS Class      : BestEffort
```

This means the Cart container was running successfully when the output was captured.

The container had:

```text id="f2c7mx"
Image : vanimina/cart:1.0.0
Ready : true
Started : true
Restarts : 0
```

---

# ⚠️ BestEffort QoS

The Pod currently has:

```yaml id="y9r4kx"
resources: {}
```

Because no CPU or memory requests/limits are defined, Kubernetes classifies this Pod as:

```text
QoS Class: BestEffort
```

For production workloads, it is better to define resource requests and limits:

```yaml id="z3m7qp"
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

This is also particularly important for your **Cart HPA**, because CPU-based HPA utilization is calculated relative to CPU requests.

---

# 📊 Pod + Service + HPA

The complete Cart runtime architecture is:

```text
                         📊 Metrics
                             │
                             ▼
                      ┌─────────────┐
                      │  Cart HPA   │
                      │   1 → 10    │
                      └──────┬──────┘
                             │
                             ▼
                      Deployment/cart
                             │
                             ▼
                         ReplicaSet
                             │
                    ┌────────┴────────┐
                    ▼                 ▼
                Cart Pod 1        Cart Pod 2
                    │                 │
                    └────────┬────────┘
                             │
                             ▼
                      Cart Service
                         cart:8080
```

The HPA scales the **Deployment**, not the Pod directly.

The Deployment then adjusts the ReplicaSet, which creates or removes Pods.

---

# 🔄 GitOps Relationship

The Pod is **not normally the direct GitOps resource**.

The desired state is managed through:

```text id="u8k3rm"
GitHub
   │
   ▼
Argo CD
   │
   ▼
Deployment/cart
   │
   ▼
ReplicaSet
   │
   ▼
Cart Pod
```

This is an important GitOps principle:

> **Declare the desired workload in Git; let Kubernetes controllers create and manage runtime resources.**

---

# 🧹 Runtime Fields Removed

Your live Pod contains many fields generated by Kubernetes.

These should normally **not be committed to Git**:

```text id="p3r8nx"
creationTimestamp
generateName
generation
resourceVersion
uid
ownerReferences
nodeName
pod IP
hostIP
containerID
imageID
status
conditions
containerStatuses
startTime
qosClass
pod-template-hash
```

The following are also generated automatically:

```text id="f8m2qc"
kube-api-access-gtwrb
```

and its projected ServiceAccount volume.

---

# 🚫 Should You Commit the Pod YAML?

**Normally, no.**

For your `cart-argocd` Helm/GitOps repository, keep the desired workload configuration in the Deployment:

```text id="c6x9mv"
templates/
├── configmap.yaml
├── service.yaml
├── deployment.yaml
└── hpa.yaml
```

Kubernetes automatically generates:

```text id="q7m3kp"
Deployment
    │
    ▼
ReplicaSet
    │
    ▼
Pod
```

Therefore, you normally do **not** need:

```text id="r8v2nx"
❌ replicaset.yaml
❌ pod.yaml
```

---

# 🔍 Useful Commands

### View Cart Pods

```bash id="m6x3qp"
kubectl get pods -n roboshop -l app=cart
```

### Detailed Pod Information

```bash id="n8r4kc"
kubectl describe pod -n roboshop -l app=cart
```

### View Pod YAML

```bash id="w2p7mz"
kubectl get pod -n roboshop -l app=cart -o yaml
```

### View Cart Logs

```bash id="v5k9rx"
kubectl logs -n roboshop -l app=cart
```

### Execute Command in Cart Pod

```bash id="c3m8qp"
kubectl exec -it -n roboshop <cart-pod> -- sh
```

### Check Pod IP and Node

```bash id="x7r2mk"
kubectl get pods -n roboshop -l app=cart -o wide
```

### Check Cart Service

```bash id="q4n8vz"
kubectl get svc cart -n roboshop
```

---

# 📌 Key Points

* 🛒 Pod: `cart-fbf947fd7-hnbg6` — generated runtime name
* ☸️ Namespace: `roboshop`
* 📦 Image: `vanimina/cart:1.0.0`
* ⚙️ Configuration: `ConfigMap/cart`
* 🌐 Service access: `cart:8080`
* 🏷️ Labels: `app=cart`, `project=roboshop`, `tier=app`
* 🏗️ Created by `ReplicaSet/cart-fbf947fd7`
* 🔄 ReplicaSet is managed by `Deployment/cart`
* 📈 HPA scales the Deployment, not the Pod directly
* 🖥️ Pod was scheduled on `desktop-worker2`
* 🩺 Current captured state: `Running` and `Ready`
* ⚠️ Current QoS: `BestEffort`
* 🛠️ CPU/memory requests and limits are recommended
* 🚫 Pod YAML normally should not be maintained separately in GitOps
--------------------------------------------------------------------------------------------------------------------------
