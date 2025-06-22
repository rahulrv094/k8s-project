## 🔹 Kubernetes Interview Q\&A Guide (Basic to Advanced)

---

### 🔵 BASICS (Q1–Q10)

#### 1. What is Kubernetes?

**Answer:** Kubernetes (K8s) is an open-source container orchestration platform for automating deployment, scaling, and management of containerized applications.

**Example:** Instead of manually starting and managing Docker containers across multiple machines, Kubernetes schedules and manages them for you.

---

#### 2. What is a Pod in Kubernetes?

**Answer:** A Pod is the smallest deployable unit in Kubernetes. It encapsulates one or more containers that share network and storage resources.

**Example:** Deploying a web server and a logging agent in the same Pod allows them to share IP and volume.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-log-pod
spec:
  containers:
  - name: nginx
    image: nginx
  - name: fluentd
    image: fluentd
```

---

#### 3. What is a Node?

**Answer:** A Node is a physical or virtual machine on which Kubernetes runs Pods.

**Example:** A cluster with 3 nodes may have 1 control plane node and 2 worker nodes.

```bash
kubectl get nodes
```

---

#### 4. What is a Cluster?

**Answer:** A Kubernetes cluster is a set of nodes managed by the control plane to run containerized applications.

**Example:** A production cluster with 1 master and 5 worker nodes handling microservices.

---

#### 5. What is kubelet?

**Answer:** The kubelet is an agent that runs on each node and ensures containers are running in a Pod as defined.

```bash
systemctl status kubelet
```

---

#### 6. What is a Deployment?

**Answer:** A Deployment manages the desired state of application Pods, allowing rollouts and rollbacks.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: myapp:latest
```

```bash
kubectl apply -f webapp-deploy.yaml
```

---

#### 7. What is a Service in Kubernetes?

**Answer:** A Service exposes a set of Pods using a stable endpoint, enabling communication across the cluster.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-svc
spec:
  selector:
    app: backend
  ports:
  - port: 80
    targetPort: 8080
  type: ClusterIP
```

---

#### 8. Types of Services

| Type         | Description                       | Example                                                |
| ------------ | --------------------------------- | ------------------------------------------------------ |
| ClusterIP    | Internal-only access (default)    | Frontend -> Backend                                    |
| NodePort     | Exposes via <NodeIP>:<Port>       | [http://192.168.1.10:30080](http://192.168.1.10:30080) |
| LoadBalancer | External LB for cloud deployments | AWS ELB to expose app                                  |
| ExternalName | Maps to external DNS              | db.external.company.com                                |

---

#### 9. What is a ConfigMap?

**Answer:** A ConfigMap is used to inject non-sensitive configuration data into Pods.

```bash
kubectl create configmap app-config --from-literal=APP_MODE=prod
```

```yaml
envFrom:
- configMapRef:
    name: app-config
```

---

#### 10. What is a Secret?

**Answer:** A Secret stores sensitive data like passwords or tokens, encoded in base64.

```bash
kubectl create secret generic db-secret --from-literal=username=admin --from-literal=password=secret123
```

```yaml
env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: username
```

---

### 🔶 INTERMEDIATE (Q11–Q20)

#### 11. How do you scale a Deployment?

```bash
kubectl scale deployment my-app --replicas=5
```

---

#### 12. What is a ReplicaSet?

**Answer:** A ReplicaSet ensures a specific number of identical Pods are running at any time. Managed by a Deployment.

---

#### 13. What is a StatefulSet?

**Answer:** A StatefulSet is used for stateful applications needing stable identities and persistent storage.

**Example:** MongoDB cluster where each Pod must retain its hostname and storage.

---

#### 14. What is a DaemonSet?

**Answer:** A DaemonSet ensures a copy of a Pod runs on all or selected nodes.

**Use Case:** Node monitoring, log collection agents like Fluentd.

---

#### 15. Job vs. CronJob

| Resource | Purpose                    | Example                |
| -------- | -------------------------- | ---------------------- |
| Job      | One-time task              | Data migration script  |
| CronJob  | Scheduled tasks using cron | Daily DB backup at 2AM |

---

#### 16. What is etcd?

**Answer:** A distributed key-value store used as Kubernetes' backing store for all cluster data.

```bash
ETCDCTL_API=3 etcdctl snapshot save /backup/etcd-snapshot.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

---

#### 17. RollingUpdate vs Recreate

| Strategy      | Description                           |
| ------------- | ------------------------------------- |
| RollingUpdate | Update Pods gradually (zero downtime) |
| Recreate      | Delete all old Pods before new ones   |

---

#### 18. Rollout & Rollback

```bash
kubectl rollout history deployment/my-deployment
kubectl rollout undo deployment/my-deployment
```

---

#### 19. What is Helm?

**Answer:** Helm is a package manager for Kubernetes. It simplifies deploying and managing Kubernetes apps.

```bash
helm install my-app bitnami/nginx
```

---

#### 20. What is a Namespace?

**Answer:** A way to partition a Kubernetes cluster into multiple virtual clusters.

```bash
kubectl create namespace staging
```

---

### 🔴 ADVANCED + DEVOPS SCENARIOS (Q21–30 + Bonus)

#### 21. How do you manage Secrets securely?

* Encrypt Secrets at rest
* Use tools like SealedSecrets or External Secrets Operator
* Avoid hardcoding in YAML files

---

#### 22. Troubleshooting CrashLoopBackOff

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/sh
```

Check probes, missing configs, bad secrets, or app errors.

---

#### 23. Liveness vs. Readiness Probes

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```

* Liveness: Restarts dead containers
* Readiness: Waits before serving traffic

---

#### 24. What is Ingress?

**Answer:** Ingress manages external HTTP/S access to services.

```yaml
host: app.example.com
  paths:
  - path: /
    backend:
      service:
        name: web-service
        port:
          number: 80
```

---

#### 25. What is Taint & Toleration?

```bash
kubectl taint nodes node1 dedicated=ml:NoSchedule
```

```yaml
tolerations:
- key: dedicated
  value: ml
  effect: NoSchedule
```

---

#### 26. What are PV and PVC?

**Answer:**

* PV = actual disk resource
* PVC = user's request for storage

```yaml
resources:
  requests:
    storage: 10Gi
```

---

#### 27. How does Kubernetes handle resource limits?

```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

---

#### 28. How to Upgrade a Kubernetes Cluster? (kubeadm)

1. **Backup etcd** (see Q16)
2. **Drain node**

```bash
kubectl drain <node-name> --ignore-daemonsets
```

3. **Upgrade kubeadm on control plane**

```bash
apt-get install -y kubeadm=1.28.0-00
kubeadm upgrade apply v1.28.0
```

4. **Upgrade kubelet & kubectl on nodes**

```bash
apt-get install -y kubelet=1.28.0-00 kubectl=1.28.0-00
systemctl restart kubelet
kubectl uncordon <node-name>
```

---

#### 29. Monitoring Kubernetes

Tools:

* Prometheus + Grafana
* Metrics Server
* Loki/ELK for logs

```bash
helm install kps prometheus-community/kube-prometheus-stack
```

---

#### 30. Securing Kubernetes

* RBAC
* NetworkPolicies
* Pod Security Standards
* Audit Logging
* TLS for all communication

```yaml
kind: RoleBinding
...
```
