roboshop catalogue argocd deployment:
-----------------------------------

Please clone below repository catalogue-argocd 

`https://github.com/iam-vanimina/catalogue-argocd.git `



`cd  /catalogue-argocd`

Make changes helm values as per your tags or version or image url etc ..

Make changes in application.yaml (mention your github repo url and create k8s roboshop namespace )

`kubectl apply -f application.yaml  `

In my case my github repo is 

`https://github.com/iam-vanimina/catalogue-argocd.git `

github repo act as the truth for the argocd.
----------------------------------------------------------------------------------------------------------------------------

# 📦 Catalogue Service – Argo CD Application

The **Catalogue Service** is deployed and continuously managed using **Argo CD + Helm + Kubernetes** following the GitOps approach.

Argo CD monitors the Catalogue Git repository and automatically synchronizes the Kubernetes resources whenever changes are committed to the `main` branch.

---

## 🔹 Argo CD Application Configuration

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: catalogue
  namespace: argocd
spec:
  project: roboshop

  source:
    repoURL: https://github.com/iam-vanimina/catalogue-argocd.git
    path: .
    targetRevision: main

    helm:
      valueFiles:
        - values.yaml
      releaseName: catalogue

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

## 📋 Configuration Details

| Configuration        | Value              | Description                                                 |
| -------------------- | ------------------ | ----------------------------------------------------------- |
| 🏷️ Application      | `catalogue`        | Argo CD Application name                                    |
| 📁 Project           | `roboshop`         | Argo CD project                                             |
| 🔗 Repository        | `catalogue-argocd` | Git repository containing Catalogue Helm chart              |
| 🌿 Branch            | `main`             | Git branch monitored by Argo CD                             |
| 📂 Path              | `.`                | Helm chart located at repository root                       |
| 📄 Values File       | `values.yaml`      | Helm configuration values                                   |
| 📦 Release Name      | `catalogue`        | Helm release name                                           |
| ☸️ Namespace         | `roboshop`         | Kubernetes deployment namespace                             |
| 🔄 Auto Sync         | Enabled            | Argo CD automatically synchronizes changes                  |
| 🧹 Prune             | Enabled            | Removes resources deleted from Git                          |
| 🛠️ Self Heal        | Enabled            | Reverts manual Kubernetes changes                           |
| 🚀 Server-Side Apply | Enabled            | Uses Kubernetes server-side apply                           |
| ⏭️ Prune Last        | Enabled            | Deletes obsolete resources after applying desired resources |

---

## 🔄 GitOps Deployment Flow

```text
Developer
    │
    │ Git Push
    ▼
GitHub Repository
catalogue-argocd
    │
    │ Argo CD monitors
    ▼
┌───────────────────────┐
│       Argo CD         │
│                       │
│  Application:        │
│  catalogue            │
│                       │
│  Helm Values:         │
│  values.yaml          │
└───────────┬───────────┘
            │
            │ Helm Render
            ▼
┌───────────────────────┐
│     Kubernetes        │
│       roboshop        │
│                       │
│  Deployment           │
│  Service              │
│  ConfigMap            │
│  HPA                  │
│  Other Resources      │
└───────────────────────┘
```

---

## 🔁 Continuous Reconciliation

Argo CD continuously compares:

```text
Git Repository
      │
      │ Desired State
      ▼
    Argo CD
      │
      │ Compare
      ▼
Kubernetes Cluster
      │
      │ Actual State
      ▼
   Sync / Heal
```

### `selfHeal: true`

If someone manually changes a Kubernetes resource:

```bash
kubectl edit deployment catalogue -n roboshop
```

Argo CD detects the difference and restores the configuration defined in Git.

### `prune: true`

If a Kubernetes resource is removed from the Helm configuration/Git repository, Argo CD can automatically remove the corresponding resource from the cluster.

---

## ⚙️ Sync Options

### `CreateNamespace=true`

Allows Argo CD to create the destination namespace if it does not already exist.

```yaml
- CreateNamespace=true
```

---

### `ApplyOutOfSyncOnly=true`

Argo CD applies only resources that are detected as **OutOfSync**, reducing unnecessary operations.

```yaml
- ApplyOutOfSyncOnly=true
```

---

### `ServerSideApply=true`

Uses Kubernetes **Server-Side Apply** instead of traditional client-side apply.

```yaml
- ServerSideApply=true
```

This is useful for managing resources where Kubernetes field ownership and larger manifests are important.

---

### `PruneLast=true`

Resources marked for deletion are pruned after the required resources have been applied.

```yaml
- PruneLast=true
```

This helps make synchronization safer during resource changes.

---

## 🌿 Helm Integration

Argo CD uses Helm to render the Catalogue Kubernetes manifests.

```text
catalogue-argocd/
│
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    ├── configmap.yaml
    └── hpa.yaml
```

The important configuration is:

```yaml
helm:
  valueFiles:
    - values.yaml
  releaseName: catalogue
```

Argo CD effectively performs the Helm rendering as part of synchronization:

```text
values.yaml
     │
     ▼
Helm Templates
     │
     ▼
Kubernetes YAML
     │
     ▼
Argo CD
     │
     ▼
Kubernetes
```

---

## 🔗 Repository

**Catalogue GitOps Repository**

```text
https://github.com/iam-vanimina/catalogue-argocd.git
```

Argo CD monitors:

```text
Branch: main
Path:   .
```

---

## 🛠️ Argo CD Commands

### Check Application

```bash
argocd app get catalogue
```

### List Applications

```bash
argocd app list
```

### Manually Sync

```bash
argocd app sync catalogue
```

### Check Application Resources

```bash
argocd app resources catalogue
```

### Check Differences

```bash
argocd app diff catalogue
```

### View Deployment History

```bash
argocd app history catalogue
```

---

## ☸️ Kubernetes Verification

Check Catalogue resources:

```bash
kubectl get all -n roboshop -l app=catalogue
```

Check Deployment:

```bash
kubectl get deployment catalogue -n roboshop
```

Check Pods:

```bash
kubectl get pods -n roboshop -l app=catalogue
```

Check Service:

```bash
kubectl get svc catalogue -n roboshop
```

Check HPA:

```bash
kubectl get hpa catalogue -n roboshop
```

---

## 🏗️ Catalogue GitOps Architecture

```text
                    ┌─────────────────────┐
                    │       GitHub        │
                    │  catalogue-argocd   │
                    │                     │
                    │  values.yaml        │
                    │  Helm Templates     │
                    └──────────┬──────────┘
                               │
                               │ Git
                               ▼
                    ┌─────────────────────┐
                    │       Argo CD       │
                    │                     │
                    │ Application:        │
                    │ catalogue           │
                    └──────────┬──────────┘
                               │
                               │ Helm Render
                               ▼
                    ┌─────────────────────┐
                    │     Kubernetes      │
                    │      roboshop       │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              ▼                ▼                ▼
        ┌───────────┐    ┌───────────┐    ┌───────────┐
        │Deployment │    │  Service  │    │    HPA    │
        │ catalogue │    │ catalogue │    │ catalogue │
        └─────┬─────┘    └───────────┘    └───────────┘
              │
              ▼
        ┌───────────┐
        │ ReplicaSet│
        └─────┬─────┘
              │
              ▼
        ┌───────────┐
        │    Pod    │
        │ catalogue │
        └───────────┘
```

---

## ✅ Key Benefits

* 🚀 Automated Kubernetes deployments
* 🔄 Continuous GitOps reconciliation
* 🛠️ Automatic self-healing
* 🧹 Automatic resource pruning
* 📦 Helm-based application deployment
* 🌿 Git-based version control
* 🔍 Easy drift detection
* ↩️ Easier rollback through Git history
* 🔐 Declarative infrastructure management
* 📈 Consistent deployment process across environments

### 🎯 GitOps Principle

> **Git is the single source of truth.**

```text
Git
 │
 │ Desired State
 ▼
Argo CD
 │
 │ Reconciliation
 ▼
Kubernetes
 │
 │ Actual State
 ▼
Catalogue Application
```
-------------------------------------------------------------------------------------------------------------------------

# ⚙️ Catalogue Service – ConfigMap

The **Catalogue ConfigMap** stores non-sensitive configuration used by the Catalogue application.

## 📄 Kubernetes Manifest

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  name: catalogue
  namespace: roboshop
  annotations:
    argocd.argoproj.io/tracking-id: catalogue:/ConfigMap:roboshop/catalogue

data:
  MONGO: "true"
```

## 📋 Configuration

| Key     | Value    | Purpose                        |
| ------- | -------- | ------------------------------ |
| `MONGO` | `"true"` | Application configuration flag |

> **Note:** The exact behavior of `MONGO=true` is determined by the Catalogue application's code/configuration.

---

## 🔄 How It Is Used

The ConfigMap is consumed by the Catalogue Deployment using `envFrom`:

```yaml
envFrom:
  - configMapRef:
      name: catalogue
```

The configuration flow is:

```text
┌──────────────────────┐
│  Catalogue ConfigMap │
│                      │
│  MONGO=true          │
└──────────┬───────────┘
           │
           │ envFrom
           ▼
┌──────────────────────┐
│ Catalogue Deployment │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   Catalogue Pod      │
│                      │
│  Environment:        │
│  MONGO=true          │
└──────────────────────┘
```

---

## 🔐 ConfigMap vs Secret

A **ConfigMap** should contain non-sensitive configuration.

Examples:

```text
ConfigMap
├── Application flags
├── Hostnames
├── Ports
└── Feature configuration
```

Sensitive information such as passwords, tokens, and credentials should be stored in a **Kubernetes Secret** instead.

---

## 🔄 Argo CD GitOps

The Argo CD tracking annotation:

```yaml
annotations:
  argocd.argoproj.io/tracking-id: catalogue:/ConfigMap:roboshop/catalogue
```

allows Argo CD to associate this Kubernetes ConfigMap with the Catalogue application managed from Git.

```text
GitHub
   │
   │ catalogue-argocd
   ▼
Argo CD
   │
   │ Helm
   ▼
Catalogue ConfigMap
   │
   ▼
Catalogue Deployment
   │
   ▼
Catalogue Pod
```

With:

```yaml
selfHeal: true
```

Argo CD can detect configuration drift and restore the desired state from Git.

---

## 🧹 Runtime Fields Removed

The following fields from the live `kubectl get configmap catalogue -o yaml` output should **not normally be committed** to Git:

```yaml
creationTimestamp:
resourceVersion:
uid:
```

These values are generated and managed by Kubernetes.

The GitOps manifest should contain the **desired state**, not runtime metadata.

---

## 🛠️ Useful Commands

### View ConfigMap

```bash
kubectl get configmap catalogue -n roboshop
```

### View ConfigMap YAML

```bash
kubectl get configmap catalogue -n roboshop -o yaml
```

### Describe ConfigMap

```bash
kubectl describe configmap catalogue -n roboshop
```

### Check Catalogue Pod Environment

```bash
kubectl exec -n roboshop deploy/catalogue -- env | grep MONGO
```

Expected:

```text
MONGO=true
```

### Check Argo CD Application

```bash
argocd app get catalogue
```

---

## 🎯 Key Points

* ⚙️ `ConfigMap` stores non-sensitive Catalogue configuration.
* 🐳 `MONGO=true` is supplied to the application as an environment variable.
* 🔄 Argo CD manages the ConfigMap through GitOps.
* 📦 The ConfigMap is consumed by the Catalogue Deployment.
* 🔐 Secrets should be used for sensitive values.
* 🧹 Kubernetes-generated metadata should be removed from Git manifests.
* 🌿 Git remains the **source of truth** for the desired configuration.
--------------------------------------------------------------------------------------------------------------------------
# 🌐 Catalogue Service – Kubernetes Service

The **Catalogue Service** provides internal network access to the Catalogue Pods within the `roboshop` namespace.

## 📄 Kubernetes Manifest

```yaml
apiVersion: v1
kind: Service

metadata:
  name: catalogue
  namespace: roboshop
  annotations:
    argocd.argoproj.io/tracking-id: catalogue:/Service:roboshop/catalogue

spec:
  type: ClusterIP

  ports:
    - port: 8080
      targetPort: 8080
      protocol: TCP

  selector:
    app: catalogue
    project: roboshop
    tier: app
```

---

## 📋 Service Configuration

| Configuration    | Value            | Description                    |
| ---------------- | ---------------- | ------------------------------ |
| 🏷️ Service Name | `catalogue`      | Kubernetes Service name        |
| 📦 Namespace     | `roboshop`       | Application namespace          |
| 🔌 Service Port  | `8080`           | Port exposed by the Service    |
| 🎯 Target Port   | `8080`           | Port on Catalogue Pods         |
| 🌐 Type          | `ClusterIP`      | Internal Kubernetes networking |
| 🔎 Selector      | `app= catalogue` | Selects Catalogue Pods         |
| 🔄 Protocol      | `TCP`            | Network protocol               |

---

## 🔗 Service Discovery

Because this is a `ClusterIP` Service, it is accessible **inside the Kubernetes cluster**.

Other RoboShop microservices can communicate with Catalogue using:

```text
catalogue:8080
```

or the complete Kubernetes DNS name:

```text
catalogue.roboshop.svc.cluster.local:8080
```

### Example

```text
Cart Pod
   │
   │ HTTP request
   │ catalogue:8080
   ▼
┌───────────────────┐
│ Catalogue Service │
│    ClusterIP      │
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│  Catalogue Pod    │
│      :8080        │
└───────────────────┘
```

---

## 🔎 Selector Configuration

The Service identifies Catalogue Pods using:

```yaml
selector:
  app: catalogue
  project: roboshop
  tier: app
```

The Catalogue Deployment/Pods must have matching labels:

```yaml
labels:
  app: catalogue
  project: roboshop
  tier: app
```

Kubernetes then automatically creates Service endpoints for matching Pods.

You can verify them with:

```bash
kubectl get endpoints catalogue -n roboshop
```

Or, on newer Kubernetes versions:

```bash
kubectl get endpointslices -n roboshop
```

---

## 🌐 ClusterIP Networking

The live Service received the ClusterIP:

```text
10.96.196.186
```

However, this IP should **not normally be hard-coded** in the Git manifest.

Kubernetes automatically allocates the ClusterIP when the Service is created.

Applications should use the Kubernetes DNS name:

```text
catalogue:8080
```

instead of relying on:

```text
10.96.196.186:8080
```

---

## 🔄 Argo CD GitOps

The Service is managed by Argo CD through the Catalogue GitOps repository.

```text
GitHub
   │
   │ Desired State
   ▼
Argo CD
   │
   │ Sync
   ▼
Catalogue Service
   │
   │ Selector
   ▼
Catalogue Pods
```

The Argo CD tracking annotation:

```yaml
annotations:
  argocd.argoproj.io/tracking-id: catalogue:/Service:roboshop/catalogue
```

associates this Service with the Catalogue Argo CD application.

---

## 🧹 Runtime Fields Removed

The original live resource contained several Kubernetes-generated fields:

```yaml
creationTimestamp:
resourceVersion:
uid:
clusterIP:
clusterIPs:
status:
```

These are not required in the declarative GitOps manifest.

In particular, the live:

```yaml
clusterIP: 10.96.196.186
```

is generated by Kubernetes and should normally be omitted.

Other default networking fields such as:

```yaml
internalTrafficPolicy:
ipFamilies:
ipFamilyPolicy:
sessionAffinity:
```

can also be omitted when the default behavior is sufficient.

---

## 🛠️ Useful Commands

### Check Service

```bash
kubectl get svc catalogue -n roboshop
```

### View Service YAML

```bash
kubectl get svc catalogue -n roboshop -o yaml
```

### Describe Service

```bash
kubectl describe svc catalogue -n roboshop
```

### Check Endpoints

```bash
kubectl get endpoints catalogue -n roboshop
```

### Check EndpointSlices

```bash
kubectl get endpointslices -n roboshop
```

### Check Catalogue Pods

```bash
kubectl get pods -n roboshop -l app=catalogue
```

### Test DNS From Another Pod

```bash
kubectl exec -it <pod-name> -n roboshop -- nslookup catalogue
```

---

## 🏗️ Catalogue Service Architecture

```text
                  Kubernetes Cluster
                         │
                         ▼
              ┌────────────────────┐
              │ Catalogue Service  │
              │                    │
              │ Type: ClusterIP    │
              │ Port: 8080         │
              └─────────┬──────────┘
                        │
              Selector: │
          app=catalogue │
                        ▼
              ┌────────────────────┐
              │   Catalogue Pod    │
              │                    │
              │       :8080        │
              └────────────────────┘
```

## 🎯 Key Points

* 🌐 `ClusterIP` provides internal Kubernetes connectivity.
* 🔌 Catalogue listens on port `8080`.
* 🔎 Service selectors route traffic to matching Catalogue Pods.
* 🧭 Other microservices can use `catalogue:8080`.
* 🔄 Argo CD manages the Service using GitOps.
* 📌 Kubernetes automatically assigns the ClusterIP.
* 🧹 Runtime-generated fields should not normally be committed to Git.
* 🚀 The Service provides stable networking even when Catalogue Pods are recreated.
--------------------------------------------------------------------------------------------------------------------------

# 🚀 Catalogue Service – Kubernetes Deployment

The **Catalogue Deployment** manages the lifecycle of Catalogue application Pods in the `roboshop` namespace.

It provides **replica management, rolling updates, ConfigMap integration, and self-healing** through Kubernetes.

## 📄 Kubernetes Manifest

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: catalogue
  namespace: roboshop

  labels:
    app: catalogue
    project: roboshop
    tier: app

  annotations:
    argocd.argoproj.io/tracking-id: catalogue:apps/Deployment:roboshop/catalogue

spec:
  replicas: 1

  selector:
    matchLabels:
      app: catalogue
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
        app: catalogue
        project: roboshop
        tier: app

    spec:
      containers:
        - name: catalogue
          image: vanimina/catalogue:1.0.0
          imagePullPolicy: Always

          envFrom:
            - configMapRef:
                name: catalogue

          resources: {}

      restartPolicy: Always
      dnsPolicy: ClusterFirst
      terminationGracePeriodSeconds: 30

  progressDeadlineSeconds: 600
  revisionHistoryLimit: 10
```

---

## 📋 Deployment Configuration

| Configuration        | Value                      | Description                            |
| -------------------- | -------------------------- | -------------------------------------- |
| 🏷️ Name             | `catalogue`                | Deployment name                        |
| 📦 Namespace         | `roboshop`                 | Kubernetes namespace                   |
| 🔢 Replicas          | `1`                        | Desired number of Pods                 |
| 🐳 Image             | `vanimina/catalogue:1.0.0` | Catalogue container image              |
| 🔄 Pull Policy       | `Always`                   | Always checks for the image            |
| ⚙️ ConfigMap         | `catalogue`                | Supplies application configuration     |
| 🔄 Strategy          | `RollingUpdate`            | Performs zero/minimal-downtime updates |
| 📈 Max Surge         | `25%`                      | Maximum additional Pods during update  |
| 📉 Max Unavailable   | `25%`                      | Maximum unavailable Pods during update |
| ⏱️ Progress Deadline | `600s`                     | Deployment progress timeout            |
| 🗂️ Revision History | `10`                       | Previous ReplicaSets retained          |

---

## 🏗️ Deployment Architecture

```text
                 Argo CD
                    │
                    │ GitOps Sync
                    ▼
          ┌─────────────────────┐
          │ Catalogue Deployment│
          │                     │
          │ replicas: 1         │
          └──────────┬──────────┘
                     │
                     ▼
             ┌───────────────┐
             │   ReplicaSet  │
             │ catalogue-... │
             └───────┬───────┘
                     │
                     ▼
              ┌─────────────┐
              │ Catalogue   │
              │     Pod     │
              └──────┬──────┘
                     │
                     ▼
              Container :8080
```

---

## ⚙️ ConfigMap Integration

The Deployment loads configuration from the `catalogue` ConfigMap:

```yaml
envFrom:
  - configMapRef:
      name: catalogue
```

The ConfigMap contains:

```yaml
data:
  MONGO: "true"
```

The configuration flow is:

```text
Catalogue ConfigMap
        │
        │ envFrom
        ▼
Catalogue Deployment
        │
        ▼
Catalogue Pod
        │
        ▼
MONGO=true
```

---

## 🔄 Rolling Update Strategy

The Deployment uses:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 25%
    maxUnavailable: 25%
```

When the Catalogue image is updated:

```text
Old Version
    │
    ▼
┌──────────────┐
│ Catalogue v1 │
└──────┬───────┘
       │
       │ Deployment Update
       ▼
┌──────────────┐
│ Create New RS│
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Catalogue v2 │
└──────┬───────┘
       │
       ▼
Old ReplicaSet
scaled down
```

This allows Kubernetes to gradually replace the old Pods with new Pods.

---

## 🏷️ Labels and Selectors

The Deployment uses these labels:

```yaml
labels:
  app: catalogue
  project: roboshop
  tier: app
```

The Deployment selector must match the Pod template labels:

```yaml
selector:
  matchLabels:
    app: catalogue
    project: roboshop
    tier: app
```

and:

```yaml
template:
  metadata:
    labels:
      app: catalogue
      project: roboshop
      tier: app
```

These labels also allow the Catalogue Service to find the correct Pods.

```text
Catalogue Service
       │
       │ selector
       ▼
app=catalogue
project=roboshop
tier=app
       │
       ▼
Catalogue Pods
```

---

## 🔄 Argo CD GitOps

The Deployment is managed by Argo CD.

```yaml
annotations:
  argocd.argoproj.io/tracking-id: catalogue:apps/Deployment:roboshop/catalogue
```

GitOps flow:

```text
GitHub
  │
  │ catalogue-argocd
  ▼
Argo CD
  │
  │ Helm Render
  ▼
Deployment
  │
  ▼
ReplicaSet
  │
  ▼
Catalogue Pod
```

If the live Deployment differs from the desired configuration in Git, Argo CD detects the drift.

With:

```yaml
selfHeal: true
```

Argo CD can restore the desired state automatically.

---

## 🛡️ Self-Healing

Kubernetes also provides self-healing through the Deployment/ReplicaSet relationship.

For example, if the Catalogue Pod is deleted:

```bash
kubectl delete pod <catalogue-pod> -n roboshop
```

The ReplicaSet detects that the desired replica count is no longer satisfied and creates a replacement Pod.

```text
Pod Deleted
     │
     ▼
ReplicaSet detects
replicas < desired
     │
     ▼
New Pod Created
     │
     ▼
Catalogue Running
```

---

## 🧹 Runtime Fields Removed

The original live Deployment contains Kubernetes-generated fields that should normally **not be committed to Git**:

```yaml
creationTimestamp:
generation:
resourceVersion:
uid:
status:
deployment.kubernetes.io/revision:
```

The `status` section represents the **current runtime state**, not the desired state.

For example:

```yaml
status:
  availableReplicas: 1
  readyReplicas: 1
  replicas: 1
```

These values are generated and maintained by Kubernetes.

---

## ⚠️ Resource Requests and Limits

Your current Deployment contains:

```yaml
resources: {}
```

This means the Catalogue container has **no CPU or memory requests/limits configured**.

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

This is particularly important if you are using an HPA for the Catalogue service, because CPU-based HPA calculations rely on CPU resource requests.

---

## 🛠️ Useful Commands

### Check Deployment

```bash
kubectl get deployment catalogue -n roboshop
```

### Describe Deployment

```bash
kubectl describe deployment catalogue -n roboshop
```

### Check ReplicaSets

```bash
kubectl get rs -n roboshop -l app=catalogue
```

### Check Pods

```bash
kubectl get pods -n roboshop -l app=catalogue
```

### Check Rollout Status

```bash
kubectl rollout status deployment/catalogue -n roboshop
```

### View Rollout History

```bash
kubectl rollout history deployment/catalogue -n roboshop
```

### Rollback Deployment

```bash
kubectl rollout undo deployment/catalogue -n roboshop
```

### View Deployment YAML

```bash
kubectl get deployment catalogue -n roboshop -o yaml
```

---

## 🎯 Key Points

* 🚀 Deployment manages Catalogue application Pods.
* 🔢 Currently configured with `1` replica.
* 🐳 Uses `vanimina/catalogue:1.0.0`.
* ⚙️ Configuration comes from the `catalogue` ConfigMap.
* 🔄 Uses a `RollingUpdate` strategy.
* 🛡️ ReplicaSet provides Pod self-healing.
* 🌐 Catalogue Pods are accessed through the `catalogue` Service.
* 🔄 Argo CD manages the Deployment using GitOps.
* 🧹 Runtime-generated fields should not normally be stored in Git.
* 📈 CPU/memory requests and limits are recommended for production, especially with HPA.
---------------------------------------------------------------------------------------------------------------------------

# 📈 Catalogue Service – Horizontal Pod Autoscaler

The **Horizontal Pod Autoscaler (HPA)** automatically adjusts the number of Catalogue Pods based on CPU utilization.

## 📄 Kubernetes Manifest

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler

metadata:
  name: catalogue
  namespace: roboshop
  annotations:
    argocd.argoproj.io/tracking-id: catalogue:autoscaling/HorizontalPodAutoscaler:roboshop/catalogue

spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: catalogue

  minReplicas: 1
  maxReplicas: 10

  targetCPUUtilizationPercentage: 20
```

---

## 📋 HPA Configuration

| Configuration       |                  Value | Description                      |
| ------------------- | ---------------------: | -------------------------------- |
| 🏷️ Name            |            `catalogue` | HPA name                         |
| 🎯 Target           | `Deployment/catalogue` | Deployment controlled by HPA     |
| ⬇️ Minimum Replicas |                    `1` | Minimum number of Catalogue Pods |
| ⬆️ Maximum Replicas |                   `10` | Maximum number of Catalogue Pods |
| 🧮 CPU Target       |                  `20%` | Target average CPU utilization   |
| 📦 API Version      |       `autoscaling/v1` | HPA API version                  |

---

## 🔄 How HPA Works

The HPA monitors CPU utilization and adjusts the number of Pods.

```text
                 CPU Utilization
                       │
                       ▼
              ┌─────────────────┐
              │      HPA        │
              │                 │
              │ Target: 20%     │
              │ Min: 1          │
              │ Max: 10         │
              └────────┬────────┘
                       │
             Scale based on CPU
                       │
                       ▼
             ┌──────────────────┐
             │ Catalogue        │
             │ Deployment       │
             └────────┬─────────┘
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
          Pod 1     Pod 2    Pod 3
```

### When CPU increases

```text
CPU > 20%
    │
    ▼
HPA increases replicas
    │
    ▼
1 → 2 → 3 → ... → 10
```

### When CPU decreases

```text
CPU < target
    │
    ▼
HPA can reduce replicas
    │
    ▼
10 → 9 → ... → 1
```

The HPA will never scale below `1` or above `10` based on this configuration.

---

# ⚠️ Current HPA Problem

Your live HPA reports:

```text
ScalingActive: False
Reason: FailedGetResourceMetric
```

and:

```text
unable to fetch metrics from resource metrics API:
the server could not find the requested resource
(get pods.metrics.k8s.io)
```

This means the HPA **cannot currently obtain CPU metrics from the Kubernetes Metrics API**.

### The problem is:

```text
HPA
 │
 │ CPU metrics request
 ▼
Metrics API
 │
 │ ❌ unavailable
 ▼
pods.metrics.k8s.io
```

Therefore, the HPA cannot calculate the desired replica count.

---

# 🔍 Check Metrics Server

Run:

```bash
kubectl get deployment metrics-server -n kube-system
```

Then check the Metrics API:

```bash
kubectl get apiservice v1beta1.metrics.k8s.io
```

Check whether the API is responding:

```bash
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes"
```

And:

```bash
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/pods"
```

---

## 📊 Test `kubectl top`

Try:

```bash
kubectl top nodes
```

and:

```bash
kubectl top pods -n roboshop
```

If you receive an error similar to:

```text
error: Metrics API not available
```

then the Metrics Server / Metrics API needs to be fixed before the HPA can work correctly.

---

# ⚠️ CPU Requests Are Important

Your Catalogue Deployment currently has:

```yaml
resources: {}
```

Therefore, there are **no CPU requests** configured.

For CPU-based HPA, it is recommended to define CPU requests.

For example:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

Then the HPA can calculate CPU utilization relative to the requested CPU.

```text
Catalogue Pod
      │
      ├── CPU Request: 100m
      │
      ├── CPU Usage: 50m
      │
      ▼
CPU Utilization = 50%
```

With a target of `20%`, the HPA would consider this Pod above the target and may scale out, subject to HPA behavior and stabilization rules.

---

# 🔗 HPA + Deployment Relationship

Your HPA explicitly targets:

```yaml
scaleTargetRef:
  apiVersion: apps/v1
  kind: Deployment
  name: catalogue
```

So the hierarchy is:

```text
                    HPA
                     │
                     │ scales
                     ▼
            ┌─────────────────┐
            │ Catalogue       │
            │ Deployment      │
            └────────┬────────┘
                     │
                     ▼
                ReplicaSet
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
        Pod 1      Pod 2      Pod 3
```

The HPA changes the Deployment's desired replica count. The Deployment/ReplicaSet then creates or removes Pods.

---

# 🔄 Argo CD GitOps

The HPA is managed through Argo CD:

```yaml
annotations:
  argocd.argoproj.io/tracking-id: catalogue:autoscaling/HorizontalPodAutoscaler:roboshop/catalogue
```

The GitOps flow is:

```text
GitHub
   │
   │ Helm values/templates
   ▼
Argo CD
   │
   │ Sync
   ▼
Catalogue HPA
   │
   │ monitors
   ▼
Catalogue Deployment
   │
   ▼
Catalogue Pods
```

Argo CD manages the **desired HPA configuration**, while the HPA controller manages the runtime replica count.

---

# 🧹 Runtime Fields Removed

The live HPA contains several fields that should not normally be committed to Git:

```yaml
creationTimestamp:
resourceVersion:
uid:
status:
autoscaling.alpha.kubernetes.io/conditions:
autoscaling.alpha.kubernetes.io/current-metrics:
```

The `status` section is runtime information generated by the HPA controller.

For example:

```yaml
status:
  currentReplicas: 1
  desiredReplicas: 0
```

should not be included in the Git manifest.

The same applies to the generated HPA condition annotations.

---

# 🛠️ Useful HPA Commands

### Check HPA

```bash
kubectl get hpa catalogue -n roboshop
```

### Watch HPA

```bash
kubectl get hpa catalogue -n roboshop -w
```

### Describe HPA

```bash
kubectl describe hpa catalogue -n roboshop
```

### Check Deployment

```bash
kubectl get deployment catalogue -n roboshop
```

### Check Pods

```bash
kubectl get pods -n roboshop -l app=catalogue
```

### Check CPU Metrics

```bash
kubectl top pods -n roboshop -l app=catalogue
```

---

# 🎯 Key Points

* 📈 HPA automatically scales the Catalogue Deployment.
* ⬇️ Minimum replicas = `1`.
* ⬆️ Maximum replicas = `10`.
* 🧮 CPU target = `20%`.
* 🎯 HPA targets `Deployment/catalogue`.
* ⚠️ Your current HPA cannot obtain CPU metrics because `metrics.k8s.io` is unavailable.
* 📊 Metrics Server must be working for CPU-based HPA.
* ⚙️ CPU requests should be configured on the Catalogue Deployment.
* 🔄 Argo CD manages the HPA declaratively through GitOps.
* 🧹 Runtime HPA status and generated annotations should not be committed to Git.
--------------------------------------------------------------------------------------------------------------------------

# 🔄 Catalogue Service – ReplicaSet

A **ReplicaSet** ensures that the desired number of Catalogue Pods are running.

The ReplicaSet below was automatically created by the `catalogue` Deployment.

## 📄 ReplicaSet Manifest

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: catalogue-85dcb45b77
  namespace: roboshop

  labels:
    app: catalogue
    project: roboshop
    tier: app

  annotations:
    argocd.argoproj.io/tracking-id: catalogue:apps/Deployment:roboshop/catalogue

spec:
  replicas: 1

  selector:
    matchLabels:
      app: catalogue
      project: roboshop
      tier: app
      pod-template-hash: 85dcb45b77

  template:
    metadata:
      labels:
        app: catalogue
        project: roboshop
        tier: app
        pod-template-hash: 85dcb45b77

    spec:
      containers:
        - name: catalogue
          image: vanimina/catalogue:1.0.0
          imagePullPolicy: Always

          envFrom:
            - configMapRef:
                name: catalogue

          resources: {}

      restartPolicy: Always
      dnsPolicy: ClusterFirst
      terminationGracePeriodSeconds: 30
```

> ⚠️ **Important:** This is an illustrative representation of the live ReplicaSet. The `pod-template-hash` and ReplicaSet name are generated by the Deployment and should **not be hard-coded in your Helm/GitOps configuration**.

---

## 📋 ReplicaSet Configuration

| Configuration | Value                      | Description                        |
| ------------- | -------------------------- | ---------------------------------- |
| 🏷️ Name      | `catalogue-85dcb45b77`     | Generated ReplicaSet name          |
| 📦 Namespace  | `roboshop`                 | Application namespace              |
| 🔢 Replicas   | `1`                        | Desired Catalogue Pods             |
| 🐳 Image      | `vanimina/catalogue:1.0.0` | Catalogue container image          |
| ⚙️ ConfigMap  | `catalogue`                | Application configuration          |
| 🔄 Owner      | `Deployment/catalogue`     | Deployment manages this ReplicaSet |
| 🏷️ Hash      | `85dcb45b77`               | Generated Pod template hash        |

---

## 🏗️ Deployment → ReplicaSet → Pod

The ReplicaSet sits between the Deployment and the Pods:

```text
              Argo CD
                 │
                 ▼
       ┌───────────────────┐
       │ Catalogue         │
       │ Deployment        │
       └─────────┬─────────┘
                 │
                 │ creates/manages
                 ▼
       ┌───────────────────┐
       │ Catalogue         │
       │ ReplicaSet        │
       │                   │
       │ replicas: 1       │
       └─────────┬─────────┘
                 │
                 │ creates/maintains
                 ▼
       ┌───────────────────┐
       │ Catalogue Pod     │
       │                   │
       │ catalogue:1.0.0   │
       └───────────────────┘
```

---

## 🔄 ReplicaSet Self-Healing

The ReplicaSet continuously ensures that the desired number of Pods exists.

For this application:

```yaml
replicas: 1
```

If the Catalogue Pod is deleted:

```text
Catalogue Pod
     │
     │ ❌ Deleted
     ▼
ReplicaSet detects
replicas < 1
     │
     ▼
New Catalogue Pod
     │
     ▼
replicas = 1
```

Example:

```bash
kubectl delete pod <catalogue-pod> -n roboshop
```

The ReplicaSet will automatically create a replacement Pod.

---

## 🔢 Replica Count

The current configuration is:

```yaml
spec:
  replicas: 1
```

This means the ReplicaSet attempts to maintain:

```text
Desired Pods = 1
```

When the Catalogue HPA scales the Deployment, the ReplicaSet receives the updated desired replica count through the Deployment.

For example:

```text
HPA
 │
 │ scale Deployment
 ▼
Deployment
 │
 │ desired replicas = 3
 ▼
ReplicaSet
 │
 ├── Catalogue Pod 1
 ├── Catalogue Pod 2
 └── Catalogue Pod 3
```

---

## 🏷️ Pod Template Hash

The live ReplicaSet contains:

```yaml
pod-template-hash: 85dcb45b77
```

This value is automatically generated by Kubernetes.

It allows the Deployment to distinguish between different Pod templates during rolling updates.

For example:

```text
Old ReplicaSet
catalogue-1111111111
        │
        ▼
Catalogue v1

New ReplicaSet
catalogue-2222222222
        │
        ▼
Catalogue v2
```

The hash changes when the Pod template changes.

### ❌ Do not manually manage this

Do not create your Helm template like:

```yaml
pod-template-hash: 85dcb45b77
```

Instead, let the Deployment controller generate and manage it.

---

## 🔄 Rolling Updates

Your Catalogue Deployment uses:

```yaml
strategy:
  type: RollingUpdate
```

When the image changes:

```text
Current Deployment
        │
        ▼
ReplicaSet v1
        │
        │ Update image
        ▼
New ReplicaSet v2
        │
        ▼
New Catalogue Pods
        │
        ▼
Old ReplicaSet scaled down
```

This allows Kubernetes to gradually replace old Pods with new Pods.

---

## ⚙️ ConfigMap Integration

The ReplicaSet's Pod template references the Catalogue ConfigMap:

```yaml
envFrom:
  - configMapRef:
      name: catalogue
```

The configuration flow is:

```text
Catalogue ConfigMap
        │
        ▼
ReplicaSet Pod Template
        │
        ▼
Catalogue Pod
        │
        ▼
Environment Variables
```

---

## 🔄 Argo CD Relationship

The ReplicaSet has the Argo CD tracking annotation:

```yaml
annotations:
  argocd.argoproj.io/tracking-id: catalogue:apps/Deployment:roboshop/catalogue
```

However, the **Deployment is the primary declarative resource** in your GitOps setup.

The normal GitOps hierarchy is:

```text
Git
 │
 ▼
Helm
 │
 ▼
Argo CD
 │
 ▼
Deployment
 │
 └── ReplicaSet
       │
       └── Pod
```

You generally do **not** need a separate ReplicaSet YAML in your Git repository.

---

## 🧹 Runtime Fields Removed

The live ReplicaSet contains Kubernetes-generated fields such as:

```yaml
creationTimestamp:
generation:
resourceVersion:
uid:
ownerReferences:
status:
deployment.kubernetes.io/desired-replicas:
deployment.kubernetes.io/max-replicas:
deployment.kubernetes.io/revision:
```

These represent runtime/controller state and should not normally be committed to Git.

The `ownerReferences` specifically show that:

```text
Deployment/catalogue
        │
        ▼
ReplicaSet/catalogue-85dcb45b77
```

The Deployment owns the ReplicaSet.

---

## 🛠️ Useful Commands

### List Catalogue ReplicaSets

```bash
kubectl get rs -n roboshop -l app=catalogue
```

### Describe ReplicaSet

```bash
kubectl describe rs catalogue-85dcb45b77 -n roboshop
```

### Check ReplicaSet Pods

```bash
kubectl get pods -n roboshop -l app=catalogue
```

### View ReplicaSet YAML

```bash
kubectl get rs catalogue-85dcb45b77 -n roboshop -o yaml
```

### Check Deployment Ownership

```bash
kubectl describe deployment catalogue -n roboshop
```

---

## 🎯 Key Points

* 🔄 ReplicaSet maintains the desired number of Catalogue Pods.
* 🔢 Current desired replicas = `1`.
* 🚀 ReplicaSet is created and managed by `Deployment/catalogue`.
* 🛡️ ReplicaSet provides Pod self-healing.
* 🏷️ `pod-template-hash` is automatically generated.
* 🔄 Deployment creates new ReplicaSets during rolling updates.
* ⚙️ Catalogue configuration comes from the `catalogue` ConfigMap.
* 📈 HPA can change the Deployment's replica count, which affects the ReplicaSet.
* 🌿 Argo CD manages the Deployment declaratively through GitOps.
* ❌ Do not normally maintain ReplicaSet YAML separately in your Git repository.
-------------------------------------------------------------------------------------------------------------------------
# 🐳 Catalogue Service – Pod

A **Pod** is the smallest deployable unit in Kubernetes. It runs the Catalogue container and is created and managed by the **ReplicaSet**, which is itself managed by the **Catalogue Deployment**.

## 📄 Kubernetes Pod Manifest

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: catalogue
  namespace: roboshop
  labels:
    app: catalogue
    project: roboshop
    tier: app

spec:
  containers:
    - name: catalogue
      image: vanimina/catalogue:1.0.0
      imagePullPolicy: Always

      envFrom:
        - configMapRef:
            name: catalogue

      resources: {}

  restartPolicy: Always
  dnsPolicy: ClusterFirst
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
```

---

## 📋 Pod Configuration

| Configuration      | Value                      | Description                                  |
| ------------------ | -------------------------- | -------------------------------------------- |
| 🏷️ Pod Name       | `catalogue`                | Catalogue application Pod                    |
| 📦 Namespace       | `roboshop`                 | Kubernetes namespace                         |
| 🐳 Container       | `catalogue`                | Container running the Catalogue service      |
| 🖼️ Image          | `vanimina/catalogue:1.0.0` | Catalogue application image                  |
| 🔄 Pull Policy     | `Always`                   | Kubernetes always attempts to pull the image |
| ⚙️ Configuration   | `ConfigMap/catalogue`      | Provides application environment variables   |
| 🔁 Restart Policy  | `Always`                   | Container is restarted when it exits         |
| 🌐 DNS Policy      | `ClusterFirst`             | Uses Kubernetes cluster DNS                  |
| 👤 Service Account | `default`                  | Default namespace ServiceAccount             |

---

# 🔗 Pod Ownership

The live Pod is owned by the ReplicaSet:

```text
Deployment
    │
    ▼
ReplicaSet
catalogue-85dcb45b77
    │
    ▼
Pod
catalogue-85dcb45b77-c9l56
    │
    ▼
Container
catalogue
```

The Pod contains the generated:

```yaml
pod-template-hash: 85dcb45b77
```

This hash identifies the Pod template used by the ReplicaSet.

⚠️ **Do not manually hard-code `pod-template-hash` when creating Pods or Deployments.** Kubernetes generates and manages it automatically.

---

# ⚙️ ConfigMap Integration

The Catalogue Pod receives configuration from the `catalogue` ConfigMap:

```yaml
envFrom:
  - configMapRef:
      name: catalogue
```

The flow is:

```text
ConfigMap
catalogue
    │
    │ environment variables
    ▼
Catalogue Pod
    │
    ▼
Catalogue Container
```

For example, the ConfigMap currently contains:

```yaml
data:
  MONGO: "true"
```

The exact behavior of `MONGO=true` depends on how the Catalogue application reads this environment variable.

---

# 🌐 Kubernetes DNS

The Pod should **not** be accessed directly using its Pod IP.

Instead, applications should communicate through the Kubernetes Service:

```text
Catalogue Pod
     ▲
     │
     │ Service
     │
Catalogue Service
     │
     ▼
catalogue:8080
```

Inside the `roboshop` namespace:

```text
catalogue:8080
```

Full Kubernetes DNS name:

```text
catalogue.roboshop.svc.cluster.local:8080
```

This allows Pods to communicate using a stable Service name even when individual Pods are recreated.

---

# 🔄 Pod Lifecycle

The Pod is created by the ReplicaSet:

```text
Deployment
    │
    ▼
ReplicaSet
    │
    ├── Creates Pod
    │
    ▼
Catalogue Pod
    │
    ├── Container starts
    │
    ├── Container runs
    │
    └── Container fails?
            │
            ▼
      Restart container
```

If the Pod itself is deleted:

```text
Pod deleted
    │
    ▼
ReplicaSet detects missing replica
    │
    ▼
New Pod created
```

This provides self-healing.

---

# ⚠️ Resource Configuration

The current Pod has:

```yaml
resources: {}
```

This means no CPU or memory requests/limits are configured.

The live Pod therefore has:

```text
QoS Class: BestEffort
```

For a production workload, configure resource requests and limits:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

This is particularly important when using CPU-based **Horizontal Pod Autoscaling (HPA)** because CPU utilization is calculated relative to CPU requests.

---

# 🧩 Pod → Service → Application

The complete Catalogue communication flow is:

```text
                    Kubernetes Cluster
                           │
                           ▼
                 ┌──────────────────┐
                 │ Catalogue Service│
                 │    ClusterIP     │
                 │     :8080        │
                 └────────┬─────────┘
                          │
                          │ selector
                          ▼
                ┌──────────────────┐
                │ Catalogue Pod    │
                │                  │
                │ Container        │
                │ catalogue        │
                │ :8080            │
                └──────────────────┘
```

The Service selects Pods using:

```yaml
selector:
  app: catalogue
  project: roboshop
  tier: app
```

---

# 🔄 Argo CD GitOps

The Pod is ultimately part of the GitOps-managed Catalogue application:

```text
GitHub
   │
   │ Helm Chart
   ▼
Argo CD
   │
   │ Sync
   ▼
Deployment
   │
   ▼
ReplicaSet
   │
   ▼
Pod
   │
   ▼
Catalogue Container
```

Argo CD manages the **desired state** through the Deployment/Helm configuration.

The Pod itself is a runtime resource generated by Kubernetes.

---

# 🧹 Runtime Fields Removed

The original live Pod contains many fields generated by Kubernetes.

These should generally **not** be committed as part of the desired-state manifest:

```yaml
creationTimestamp:
generateName:
generation:
resourceVersion:
uid:
ownerReferences:
nodeName:
status:
containerStatuses:
containerID:
imageID:
hostIP:
podIP:
startTime:
qosClass:
```

The following are also generated automatically:

```text
pod-template-hash
ServiceAccount projected volume
service-account token
Pod IP
Node assignment
Container ID
Container status
Pod conditions
```

For example, these values are runtime information:

```text
Pod IP:      10.244.1.9
Node:        desktop-worker
Container ID: containerd://...
Phase:       Running
QoS Class:   BestEffort
```

They should not be hard-coded into the GitOps configuration.

---

# 🛠️ Useful Kubernetes Commands

### List Catalogue Pods

```bash
kubectl get pods -n roboshop -l app=catalogue
```

### Get Pod Details

```bash
kubectl describe pod -n roboshop -l app=catalogue
```

### Check Pod Logs

```bash
kubectl logs -n roboshop -l app=catalogue
```

### Follow Pod Logs

```bash
kubectl logs -f -n roboshop -l app=catalogue
```

### Check Pod YAML

```bash
kubectl get pod -n roboshop -l app=catalogue -o yaml
```

### Check Pod Resources

```bash
kubectl top pods -n roboshop -l app=catalogue
```

### Check Pod → ReplicaSet Ownership

```bash
kubectl get rs -n roboshop
```

### Check Catalogue Deployment

```bash
kubectl get deployment catalogue -n roboshop
```

---

# 🎯 Key Points

* 🐳 Pod runs the Catalogue container.
* 🔄 Pod is created and managed by a ReplicaSet.
* 🚀 ReplicaSet is managed by the Catalogue Deployment.
* ⚙️ Configuration is loaded from `ConfigMap/catalogue`.
* 🌐 Applications should access Catalogue through `Service/catalogue`.
* 🔗 Kubernetes DNS provides stable service discovery.
* 🩹 ReplicaSet provides self-healing when Pods disappear.
* ⚠️ `resources: {}` makes the Pod `BestEffort`.
* 📈 CPU requests are recommended when using HPA.
* 🔄 Argo CD manages the desired state through GitOps.
* 🧹 Pod runtime fields should not be committed to Git.
* ⚠️ A Pod normally should **not** be maintained as a standalone GitOps manifest when it is generated by a Deployment.
--------------------------------------------------------------------------------------------------------------------------

