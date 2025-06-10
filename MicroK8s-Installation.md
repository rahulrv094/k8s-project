
# 🧩 MicroK8s: Overview, Installation, and Alternatives

---

## 📌 What is MicroK8s?

**MicroK8s** is a **lightweight, fast, and secure Kubernetes distribution** developed by **Canonical** (creators of Ubuntu). It is ideal for:

* Local Kubernetes development
* CI/CD pipelines
* Edge computing & IoT
* Lightweight production workloads

### 🔑 Key Features:

* Single-node or multi-node Kubernetes
* Uses **containerd**, not Docker
* Built-in add-ons: DNS, Ingress, Istio, Prometheus, etc.
* Snap-based installation and auto-updates
* Minimal system footprint

---

## ✅ Prerequisites

| Requirement      | Needed | Notes                                         |
| ---------------- | ------ | --------------------------------------------- |
| Ubuntu OS        | ✅ Yes  | 18.04, 20.04, 22.04+ (ideal)                  |
| `snapd`          | ✅ Yes  | Usually pre-installed on Ubuntu               |
| Docker Installed | ❌ No   | Not required (MicroK8s uses `containerd`)     |
| Internet Access  | ✅ Yes  | Required for downloading packages and add-ons |

### 🛠 Optional Tools (Recommended)

| Tool            | Purpose                     | Install Command                            |
| --------------- | --------------------------- | ------------------------------------------ |
| `kubectl` alias | Global `kubectl` usage      | `sudo snap alias microk8s.kubectl kubectl` |
| `helm`          | Kubernetes package manager  | `sudo snap install helm --classic`         |
| `jq`            | JSON parsing tool           | `sudo apt install jq`                      |
| `git`           | Version control (for demos) | `sudo apt install git`                     |

---

## 🧰 How to Install MicroK8s on Ubuntu

### 🔹 Step 1: Install MicroK8s

```bash
sudo snap install microk8s --classic
```

### 🔹 Step 2: Add User Permissions

```bash
sudo usermod -a -G microk8s $USER
newgrp microk8s
```

> 💡 Log out and back in to apply the group changes.

### 🔹 Step 3: Check Status

```bash
microk8s status --wait-ready
```

### 🔹 Step 4: Enable Add-ons

```bash
microk8s enable dns         # Required for service discovery
microk8s enable dashboard   # Optional: Web UI
microk8s enable ingress     # Optional: External access
```

### 🔹 Step 5: Use kubectl

```bash
microk8s kubectl get nodes
```

(Optional alias)

```bash
sudo snap alias microk8s.kubectl kubectl
```

### 🔹 Step 6: Access Dashboard

```bash
microk8s dashboard-proxy
```

Open the URL shown in your browser.

---

## 🔁 Alternatives to MicroK8s

| Tool               | Description                                   | Ideal For                       |
| ------------------ | --------------------------------------------- | ------------------------------- |
| **Minikube**       | Single-node cluster with VM or Docker backend | Beginners, GUI use              |
| **Kind**           | Kubernetes in Docker                          | CI pipelines, fast testing      |
| **K3s**            | Lightweight Kubernetes by Rancher             | Edge, IoT, low-resource systems |
| **Kubeadm**        | Official CLI tool to build full K8s clusters  | Manual, production-like setup   |
| **Docker Desktop** | Built-in Kubernetes (Windows/Mac)             | Docker-centric local dev        |

---

## 🔍 MicroK8s vs Minikube vs Kind

| Feature           | MicroK8s      | Minikube        | Kind            |
| ----------------- | ------------- | --------------- | --------------- |
| Install Method    | Snap          | Binary + VM     | Docker-based    |
| Container Runtime | containerd    | Docker/CRIO     | Docker          |
| Add-ons           | Built-in      | Requires setup  | Manual          |
| OS Support        | Ubuntu/Linux  | Linux, Win, Mac | Linux, Win, Mac |
| Ideal Use         | Dev, Edge, CI | Beginners       | CI, Testing     |

---

## ✅ Summary

* **MicroK8s** is a lightweight, full Kubernetes experience without needing Docker.
* Best suited for developers, testers, or edge computing environments.
* Easy to install and manage using `snap`.
* Comes with many production-grade features out of the box.
* Can be a drop-in replacement for Minikube, especially on Ubuntu.

---
