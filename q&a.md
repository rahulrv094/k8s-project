### **1. How do you create an application in ArgoCD? What tasks are required?**

**Answer:**
To create an application in ArgoCD:

**Step 1: Pre-requisites:**

* Your application’s Kubernetes manifests must be in Git (can be plain YAML, Kustomize, Helm chart, or Jsonnet).
* ArgoCD must be connected to your Git repository and your Kubernetes cluster.

**Step 2: Create ArgoCD Application (via CLI or UI)**

Example (CLI using `argocd`):

```bash
argocd app create my-app \
  --repo https://github.com/user/repo.git \
  --path k8s \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

**Tasks Required in ArgoCD:**

* Connect Git repository to ArgoCD.
* Register the target Kubernetes cluster.
* Define the Application (Git path, target cluster/namespace).
* Sync and monitor the Application state.
* Set auto-sync or manual sync policies.
* Add RBAC and SSO for secure access.

---

### **2. Developers complain about long Docker build times. How can you optimize the image?**

**Answer:**
Optimize Docker build using the following strategies:

✅ **Use smaller base images** (e.g., `alpine`, `distroless`) to reduce size and attack surface.

```Dockerfile
FROM openjdk:17-jdk-alpine
```

✅ **Leverage multi-stage builds**:
Separate build and runtime stages to keep only what's necessary.

```Dockerfile
FROM maven:3.8.4-openjdk-17 as builder
COPY . .
RUN mvn package

FROM openjdk:17-jdk-alpine
COPY --from=builder target/app.jar /app/app.jar
```

✅ **Optimize Docker layer caching:**

* Move rarely changed layers to the top.
* Install dependencies before copying application code.

```Dockerfile
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src/ .
```

✅ **Avoid `latest` tag** in base image – causes cache busting.

✅ **Use `.dockerignore`** to exclude unnecessary files.

✅ **Use BuildKit**: `DOCKER_BUILDKIT=1 docker build .`

✅ **Parallel builds or remote cache (e.g., Buildx)** for advanced CI/CD optimizations.

---

### **3. I want to exec into the container using `bash` but it's not available. What should I do?**

**Answer:**
If `bash` is not available, try alternatives:

* Try `sh`:

```bash
kubectl exec -it pod-name -- sh
```

* **Install bash temporarily (not ideal for prod):**

```bash
kubectl exec -it pod-name -- apk add --no-cache bash
```

* **Better Approach:**
  Update the Dockerfile to include `bash` during the build process:

```Dockerfile
RUN apk add --no-cache bash
```

But for lightweight containers (like `distroless`), consider debugging using ephemeral containers or sidecar tools like `kubectl debug`.

---

### **4. What Kubernetes resources are required to deploy a backend application?**

**Answer:**
Minimum K8s resources for a backend service:

1. **Deployment** – To manage pods (replica set, updates).
2. **Service** – For exposing the backend internally or externally.
3. **ConfigMap/Secret** – For configuration or credentials.
4. **PersistentVolumeClaim** – If app needs persistent storage.
5. **Ingress (optional)** – For external HTTP/HTTPS access.

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
...
---
apiVersion: v1
kind: Service
...
```

---

### **5. When should we use StatefulSet vs Deployment?**

**Answer:**

| Feature       | Deployment                | StatefulSet                      |
| ------------- | ------------------------- | -------------------------------- |
| Pod Identity  | No unique identity        | Each pod has a stable identity   |
| Use Case      | Stateless apps (web, API) | Stateful apps (DB, Kafka, Redis) |
| Storage       | Shared or no storage      | PersistentVolume per pod         |
| Startup Order | No guarantees             | Ordered, controlled startup      |

**Use StatefulSet for:**

* Databases (MongoDB, MySQL, Cassandra)
* Queues or brokers (Kafka, RabbitMQ)
* When pod names and storage must persist across restarts.

---

### **6. How do you perform Kubernetes upgrade in a production cluster with zero downtime?**

**Answer:**
Zero-downtime K8s upgrade strategy:

✅ **Pre-check:**

* Ensure HA control plane.
* Backup etcd.
* Drain and cordon one node at a time.

✅ **Node upgrade strategy:**

1. Cordon node:

```bash
kubectl cordon <node-name>
```

2. Drain node:

```bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```

3. Upgrade kubelet & kube-proxy.
4. Uncordon:

```bash
kubectl uncordon <node-name>
```

✅ **Rolling upgrade per node:**
Use automation tools like `kubeadm`, `kOps`, or `eksctl`.

✅ **PodDisruptionBudgets** and **readiness probes** ensure services don't go down during upgrades.

---

### **7. What is a mutating webhook in Kubernetes?**

**Answer:**
A **Mutating Admission Webhook** intercepts requests to the Kubernetes API **before the object is persisted**, allowing you to **modify the object** (e.g., inject sidecars, set defaults).

📌 **Use case:** Injecting sidecar (e.g., Istio Envoy proxy), adding security policies.

Mutating webhooks run **before** validating webhooks.

---

### **8. What is a sidecar container? What is it used for?**

**Answer:**
A **sidecar** is a secondary container in a pod that supports the main application container.

**Use cases:**

* **Logging agent** (e.g., Fluentd)
* **Monitoring agent** (e.g., OpenTelemetry Collector)
* **Service mesh proxy** (e.g., Istio Envoy)
* **Secrets fetching** (e.g., Vault agent)

Example:

```yaml
containers:
  - name: app
    image: my-backend
  - name: otel-collector
    image: otel/opentelemetry-collector
```

---

### **9. How does Splunk collect logs from Kubernetes pods?**

**Answer:**
There are three common ways Splunk collects pod logs:

1. **Log Forwarder Agent** (sidecar or DaemonSet):

   * Use **Splunk Connect for Kubernetes** (includes Fluentd, HEC token).
   * Reads logs from `/var/log/containers/`.

2. **Universal Forwarder** (optional):

   * Installed inside the container or as a sidecar.

3. **HEC (HTTP Event Collector):**

   * Fluentd or collector sends logs to Splunk via HEC endpoint.

---

### **10. Why do we need OpenTelemetry Collector?**

**Answer:**
OpenTelemetry (OTel) Collector acts as a **centralized telemetry agent** that:

* **Receives** traces, metrics, and logs from applications.
* **Processes** (filter, enrich, batch).
* **Exports** data to systems like Jaeger, Prometheus, Splunk, Datadog.

✅ It decouples application code from backend observability providers.

✅ It supports **pipelines** with receivers, processors, exporters.

---

### **11. My Java app is not observable via OTel. How can I make it observable in Kubernetes?**

**Answer:**

1. **Auto-instrument Java App:**
   Add OTel Java agent during container start:

```bash
-javaagent:/otel/opentelemetry-javaagent.jar \
-Dotel.exporter.otlp.endpoint=http://otel-collector:4317 \
-Dotel.service.name=my-java-app
```

2. **Mount OTel agent in Dockerfile or via ConfigMap**

3. **Deploy OpenTelemetry Collector in K8s (as a sidecar or DaemonSet)**

4. **Ensure OTel Collector has OTLP receiver enabled**

5. **Use readiness/liveness probes to ensure app health**

---

### **12. In Terraform, I want to create a bucket only for the dev environment. How can I do that?**

**Answer:**
Use conditional resource creation with `count`:

```hcl
variable "env" {}

resource "aws_s3_bucket" "tf_state" {
  count = var.env == "dev" ? 1 : 0
  bucket = "my-tfstate-dev"
}
```

Or use `terraform.workspace`:

```hcl
count = terraform.workspace == "dev" ? 1 : 0
```

Also separate environments using workspaces or folder-based module structures (e.g., `dev/main.tf`, `prod/main.tf`).

---

### **13. I have a domain (falabale.com) and hosted `/order` path in a pod. How does browser traffic reach the pod? Explain the hop path.**

**Answer:**

#### **Traffic Path from Browser to Pod:**

1. **Browser request:**

   * URL: `https://falabale.com/order`

2. **DNS Resolution:**

   * `falabale.com` resolves to the public IP of your **LoadBalancer** or **Ingress Controller**.

3. **Ingress Controller (e.g., NGINX or ALB):**

   * Matches path `/order` and routes to the **Service**.

4. **Kubernetes Service:**

   * Forwards traffic to the backend **Pod** via kube-proxy.

5. **Pod:**

   * The container listens on its exposed port and serves the request.

💡 Ensure:

* Ingress has path-based routing.
* Service type is `ClusterIP` or `NodePort` (based on architecture).
* Pod has a matching label for the service selector.

