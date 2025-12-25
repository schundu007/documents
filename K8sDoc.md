<p align="center">
  <img src="https://kubernetes.io/images/kubernetes-horizontal-color.png" width="400" alt="Kubernetes Logo">
</p>

<h1 align="center">📘 The Complete kubectl Command Guide</h1>

<p align="center">
  <i>A Comprehensive Handbook for Mastering Kubernetes Command Line</i>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Kubernetes-v1.28+-blue?style=for-the-badge&logo=kubernetes&logoColor=white" alt="Kubernetes">
  <img src="https://img.shields.io/badge/Level-Beginner%20to%20Advanced-green?style=for-the-badge" alt="Level">
  <img src="https://img.shields.io/badge/Pages-50+-orange?style=for-the-badge" alt="Pages">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="License">
</p>

<p align="center">
  <b>First Edition • 2024</b>
</p>

---

## 📑 Table of Contents

<table>
<tr>
<td width="50%" valign="top">

### Part I: Foundations
- [Preface](#-preface)
- [About This Book](#-about-this-book)
- [Chapter 1: Introduction to Kubernetes](#-chapter-1-introduction-to-kubernetes)
- [Chapter 2: Understanding kubectl](#-chapter-2-understanding-kubectl)
- [Chapter 3: Setting Up Your Environment](#-chapter-3-setting-up-your-environment)

### Part II: Core Concepts
- [Chapter 4: Working with Pods](#-chapter-4-working-with-pods)
- [Chapter 5: Managing Nodes](#-chapter-5-managing-nodes)
- [Chapter 6: Deployments & ReplicaSets](#-chapter-6-deployments--replicasets)
- [Chapter 7: Services & Networking](#-chapter-7-services--networking)

</td>
<td width="50%" valign="top">

### Part III: Configuration & Storage
- [Chapter 8: ConfigMaps & Secrets](#-chapter-8-configmaps--secrets)
- [Chapter 9: Resource Creation Patterns](#-chapter-9-resource-creation-patterns)

### Part IV: Advanced Operations
- [Chapter 10: Rollouts & Version Control](#-chapter-10-rollouts--version-control)
- [Chapter 11: DaemonSets, Jobs & CronJobs](#-chapter-11-daemonsets-jobs--cronjobs)
- [Chapter 12: Monitoring & Observability](#-chapter-12-monitoring--observability)
- [Chapter 13: Troubleshooting Guide](#-chapter-13-troubleshooting-guide)

### Appendices
- [Appendix A: Quick Reference Card](#-appendix-a-quick-reference-card)
- [Appendix B: Shell Aliases & Productivity](#-appendix-b-shell-aliases--productivity)
- [Appendix C: Glossary](#-appendix-c-glossary)

</td>
</tr>
</table>

---

<div align="center">

# Part I
# Foundations

*"The journey of a thousand containers begins with a single kubectl command."*

</div>

---

# 📖 Preface

Welcome to **The Complete kubectl Command Guide**. 

In today's cloud-native world, Kubernetes has become the de facto standard for container orchestration. Whether you're deploying a simple web application or managing a complex microservices architecture across multiple clusters, understanding kubectl—the command-line interface to Kubernetes—is an essential skill for any DevOps engineer, platform engineer, or developer.

This book was born from countless hours of hands-on experience, debugging sessions at 2 AM, and the collective wisdom of the Kubernetes community. My goal is to provide you with not just a reference manual, but a true understanding of each command—when to use it, why it works the way it does, and how to leverage it in real-world scenarios.

### Who This Book Is For

This guide is designed for:

- **Beginners** who are just starting their Kubernetes journey
- **Intermediate users** looking to deepen their understanding
- **Experienced practitioners** who need a comprehensive reference
- **CKA/CKAD exam candidates** preparing for certification

### How to Use This Book

You can read this book in two ways:

1. **Cover to cover**: Each chapter builds upon the previous, creating a comprehensive learning path
2. **As a reference**: Jump directly to specific chapters or commands when you need them

Throughout this book, you'll find:

- 📖 **Detailed explanations** of what each command does
- 🎯 **Use cases** explaining when to use each command
- 💡 **Pro tips** from real-world experience
- ⚠️ **Warnings** about common pitfalls
- 🔥 **Best practices** for production environments

Let's begin your journey to kubectl mastery.

---

# 📖 About This Book

### Conventions Used

Throughout this book, we use the following conventions:

| Symbol | Meaning |
|--------|---------|
| `<name>` | A placeholder—replace with your actual value |
| `[optional]` | Optional parameter |
| 📖 | Explanation of what the command does |
| 🎯 | When to use this command |
| 💡 | Pro tip or best practice |
| ⚠️ | Warning or caution |
| 🔥 | Critical or frequently used |

### Command Syntax

```
kubectl [command] [TYPE] [NAME] [flags]
```

| Component | Description | Example |
|-----------|-------------|---------|
| `command` | The operation to perform | `get`, `create`, `delete` |
| `TYPE` | Resource type | `pod`, `deployment`, `service` |
| `NAME` | Resource name | `my-app`, `nginx` |
| `flags` | Optional modifiers | `-o yaml`, `-n namespace` |

---

# 📘 Chapter 1: Introduction to Kubernetes

<div align="center">
<i>"Kubernetes is not just a technology—it's a new way of thinking about infrastructure."</i>
</div>

<br>

## 1.1 What is Kubernetes?

**Kubernetes** (often abbreviated as **K8s**—the 8 represents the eight letters between 'K' and 's') is an open-source container orchestration platform originally developed by Google. It automates the deployment, scaling, and management of containerized applications.

Think of Kubernetes as the **operating system for your data center**. Just as your laptop's OS manages processes, memory, and hardware, Kubernetes manages containers, networking, and compute resources across a cluster of machines.

## 1.2 The Kubernetes Architecture

Understanding the architecture helps you understand what kubectl commands actually do.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           KUBERNETES CLUSTER                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────┐    │
│  │                        CONTROL PLANE (Master)                       │    │
│  │                                                                      │    │
│  │   ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌──────────┐  │    │
│  │   │     API     │  │  Scheduler  │  │ Controller  │  │   etcd   │  │    │
│  │   │   Server    │  │             │  │  Manager    │  │          │  │    │
│  │   │             │  │ Decides     │  │             │  │ Cluster  │  │    │
│  │   │ All kubectl │  │ which node  │  │ Ensures     │  │ state    │  │    │
│  │   │ commands go │  │ runs each   │  │ desired     │  │ database │  │    │
│  │   │ here first  │  │ pod         │  │ state       │  │          │  │    │
│  │   └─────────────┘  └─────────────┘  └─────────────┘  └──────────┘  │    │
│  │         ▲                                                           │    │
│  └─────────┼───────────────────────────────────────────────────────────┘    │
│            │                                                                 │
│            │  kubectl commands                                               │
│            │                                                                 │
│  ┌─────────┴───────────────────────────────────────────────────────────┐    │
│  │                         WORKER NODES                                 │    │
│  │                                                                      │    │
│  │   ┌──────────────────────┐      ┌──────────────────────┐           │    │
│  │   │      NODE 1          │      │      NODE 2          │           │    │
│  │   │                      │      │                      │           │    │
│  │   │  ┌────────────────┐  │      │  ┌────────────────┐  │           │    │
│  │   │  │      Pod       │  │      │  │      Pod       │  │           │    │
│  │   │  │  ┌──────────┐  │  │      │  │  ┌──────────┐  │  │           │    │
│  │   │  │  │Container │  │  │      │  │  │Container │  │  │           │    │
│  │   │  │  └──────────┘  │  │      │  │  │Container │  │  │           │    │
│  │   │  └────────────────┘  │      │  │  └──────────┘  │  │           │    │
│  │   │                      │      │  └────────────────┘  │           │    │
│  │   │  ┌────────────────┐  │      │                      │           │    │
│  │   │  │    kubelet     │  │      │  ┌────────────────┐  │           │    │
│  │   │  │   (agent)      │  │      │  │    kubelet     │  │           │    │
│  │   │  └────────────────┘  │      │  └────────────────┘  │           │    │
│  │   │                      │      │                      │           │    │
│  │   │  ┌────────────────┐  │      │  ┌────────────────┐  │           │    │
│  │   │  │   kube-proxy   │  │      │  │   kube-proxy   │  │           │    │
│  │   │  │  (networking)  │  │      │  │  (networking)  │  │           │    │
│  │   │  └────────────────┘  │      │  └────────────────┘  │           │    │
│  │   └──────────────────────┘      └──────────────────────┘           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 1.3 Core Concepts

Before diving into commands, let's establish a shared vocabulary:

### The Pod

> **Definition**: The smallest deployable unit in Kubernetes. A pod encapsulates one or more containers, storage resources, a unique network IP, and options governing how the containers should run.

**Real-world analogy**: Think of a pod as an apartment. The apartment (pod) has a unique address (IP). Inside, you might have one person (container) or roommates (multiple containers) sharing the kitchen and living room (shared storage and network).

### The Deployment

> **Definition**: A Deployment provides declarative updates for Pods and ReplicaSets. You describe a desired state, and the Deployment Controller changes the actual state to match.

**Real-world analogy**: A Deployment is like a restaurant franchise agreement. It says "I want 5 locations (replicas), each serving burgers (container image)." If one location closes (pod dies), the franchise (Deployment) opens a new one automatically.

### The Service

> **Definition**: An abstract way to expose an application running on a set of Pods as a network service.

**Real-world analogy**: A Service is like a phone number for a business. Employees (pods) come and go, but customers always dial the same number (Service IP/DNS). The phone system (kube-proxy) routes calls to available employees.

### The Node

> **Definition**: A worker machine in Kubernetes, which can be a physical or virtual machine.

**Real-world analogy**: Nodes are like servers in a data center. They provide the compute power where your applications actually run.

---

# 📘 Chapter 2: Understanding kubectl

<div align="center">
<i>"kubectl is your window into the Kubernetes world."</i>
</div>

<br>

## 2.1 What is kubectl?

**kubectl** (pronounced "kube-control," "kube-C-T-L," or affectionately "kube-cuddle") is the command-line interface for running commands against Kubernetes clusters.

When you run a kubectl command, here's what happens:

```
┌──────────────┐     HTTPS      ┌──────────────┐     ┌──────────────┐
│   kubectl    │ ──────────────▶│  API Server  │────▶│    etcd      │
│  (your CLI)  │◀────────────── │              │◀────│  (database)  │
└──────────────┘    Response    └──────────────┘     └──────────────┘
                                       │
                                       ▼
                              ┌──────────────┐
                              │   Scheduler  │
                              │  Controller  │
                              │   Manager    │
                              └──────────────┘
```

1. You type a command
2. kubectl reads your kubeconfig file for cluster credentials
3. kubectl sends an HTTPS request to the API Server
4. API Server authenticates you and processes the request
5. Results are returned and displayed

## 2.2 The kubectl Configuration File

kubectl uses a configuration file (kubeconfig) to store cluster access information. By default, it's located at `~/.kube/config`.

```yaml
# Example kubeconfig structure
apiVersion: v1
kind: Config
clusters:
  - name: production
    cluster:
      server: https://prod-cluster.example.com:6443
      certificate-authority: /path/to/ca.crt
  - name: development
    cluster:
      server: https://dev-cluster.example.com:6443
users:
  - name: admin
    user:
      client-certificate: /path/to/admin.crt
      client-key: /path/to/admin.key
contexts:
  - name: prod-admin
    context:
      cluster: production
      user: admin
      namespace: default
current-context: prod-admin
```

## 2.3 Contexts and Namespaces

### Understanding Contexts

A **context** is a combination of:
- **Cluster**: Which Kubernetes cluster to connect to
- **User**: Which credentials to use
- **Namespace**: The default namespace for commands

```bash
# List all contexts
kubectl config get-contexts

# Output:
# CURRENT   NAME         CLUSTER      AUTHINFO   NAMESPACE
# *         prod-admin   production   admin      default
#           dev-admin    development  admin      dev
```

### Switching Contexts

```bash
# Switch to a different context
kubectl config use-context dev-admin

# Verify current context
kubectl config current-context
```

### Understanding Namespaces

**Namespaces** provide a way to divide cluster resources between multiple users or projects. Think of them as folders for organizing your Kubernetes resources.

```bash
# List all namespaces
kubectl get namespaces

# Common namespaces:
# - default: Where resources go if you don't specify a namespace
# - kube-system: Kubernetes system components
# - kube-public: Publicly accessible resources
```

```bash
# Set default namespace for current context
kubectl config set-context --current --namespace=my-app

# Run a command in a specific namespace (one-time)
kubectl get pods -n kube-system
```

---

# 📘 Chapter 3: Setting Up Your Environment

<div align="center">
<i>"A craftsman is only as good as their tools—and how well they've configured them."</i>
</div>

<br>

## 3.1 Installing kubectl

### macOS

```bash
# Using Homebrew (recommended)
brew install kubectl

# Or using curl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

### Linux

```bash
# Using curl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Using apt (Debian/Ubuntu)
sudo apt-get update && sudo apt-get install -y kubectl

# Using yum (RHEL/CentOS)
sudo yum install -y kubectl
```

### Windows

```powershell
# Using Chocolatey
choco install kubernetes-cli

# Using Scoop
scoop install kubectl
```

## 3.2 Verifying Your Installation

```bash
# Check kubectl version
kubectl version --client

# Expected output:
# Client Version: v1.28.0
# Kustomize Version: v5.0.4

# Verify cluster connection
kubectl cluster-info

# Expected output:
# Kubernetes control plane is running at https://xxx.xxx.xxx.xxx:6443
# CoreDNS is running at https://xxx.xxx.xxx.xxx:6443/api/v1/...
```

## 3.3 Enabling Shell Autocompletion

Autocompletion is a game-changer for productivity. Set it up once and never look back.

### Bash

```bash
# Install bash-completion if not present
# macOS
brew install bash-completion

# Linux
apt-get install bash-completion  # Debian/Ubuntu
yum install bash-completion      # RHEL/CentOS

# Enable kubectl autocompletion
echo 'source <(kubectl completion bash)' >> ~/.bashrc

# If you use an alias for kubectl, extend completion to it
echo 'alias k=kubectl' >> ~/.bashrc
echo 'complete -o default -F __start_kubectl k' >> ~/.bashrc

# Reload your shell
source ~/.bashrc
```

### Zsh

```bash
# Enable kubectl autocompletion
echo 'source <(kubectl completion zsh)' >> ~/.zshrc

# If you use an alias
echo 'alias k=kubectl' >> ~/.zshrc
echo 'compdef k=kubectl' >> ~/.zshrc

# Reload your shell
source ~/.zshrc
```

### Fish

```bash
# Enable kubectl autocompletion
kubectl completion fish | source

# To load completions for each session
kubectl completion fish > ~/.config/fish/completions/kubectl.fish
```

---

<div align="center">

# Part II
# Core Concepts

*"Master the fundamentals, and everything else becomes possible."*

</div>

---

# 📘 Chapter 4: Working with Pods

<div align="center">
<i>"Pods are the atoms of the Kubernetes universe."</i>
</div>

<br>

## 4.1 Understanding Pods

A **Pod** is the smallest and simplest unit in the Kubernetes object model. It represents a single instance of a running process in your cluster.

### Key Characteristics

| Characteristic | Description |
|---------------|-------------|
| **Ephemeral** | Pods are mortal—they're created, live, and die |
| **Atomic** | A pod is scheduled as a single unit |
| **Shared context** | Containers in a pod share network and storage |
| **Unique IP** | Each pod gets its own IP address |

### Single-Container vs Multi-Container Pods

```
   Single-Container Pod              Multi-Container Pod
   ┌─────────────────┐              ┌─────────────────────────┐
   │       Pod       │              │          Pod            │
   │  ┌───────────┐  │              │  ┌─────┐    ┌─────┐    │
   │  │ Container │  │              │  │ App │    │ Log │    │
   │  │   (App)   │  │              │  │     │◄──▶│Sider│    │
   │  └───────────┘  │              │  └─────┘    └─────┘    │
   │                 │              │         Shared         │
   │   IP: 10.1.1.5  │              │        Storage         │
   └─────────────────┘              │                        │
                                    │      IP: 10.1.1.6      │
                                    └─────────────────────────┘
```

> 💡 **Best Practice**: Use single-container pods unless you have a specific reason for multiple containers (like sidecars for logging, proxies, or config reloading).

## 4.2 Pod Commands Reference

### Listing Pods

---

#### `kubectl get pod`

📖 **What it does**: Lists all pods in the current namespace, showing their basic status information.

🎯 **When to use**: Your go-to command for checking what's running. This is often the first command you'll run when connecting to a cluster.

**Syntax**:
```bash
kubectl get pod
kubectl get pods       # 'pod' and 'pods' are interchangeable
kubectl get po         # Short form
```

**Output**:
```
NAME                        READY   STATUS    RESTARTS   AGE
nginx-deployment-abc123     1/1     Running   0          2d
api-server-xyz789           2/2     Running   1          5h
worker-job-batch-001        0/1     Completed 0          1h
```

**Understanding the columns**:

| Column | Meaning |
|--------|---------|
| `NAME` | The unique name of the pod |
| `READY` | Containers ready / Total containers |
| `STATUS` | Current pod phase (Running, Pending, etc.) |
| `RESTARTS` | Number of container restarts |
| `AGE` | Time since pod creation |

**Common status values**:

| Status | Meaning |
|--------|---------|
| `Pending` | Pod accepted but containers not yet created |
| `Running` | Pod bound to a node, all containers started |
| `Succeeded` | All containers terminated successfully |
| `Failed` | All containers terminated, at least one failed |
| `CrashLoopBackOff` | Container keeps crashing |
| `ImagePullBackOff` | Cannot pull container image |

---

#### `kubectl get pod -o wide`

📖 **What it does**: Displays pods with additional columns including the node they're running on and their IP addresses.

🎯 **When to use**: 
- Finding which node hosts a specific pod
- Troubleshooting network connectivity issues
- Verifying pod distribution across nodes

**Syntax**:
```bash
kubectl get pod -o wide
```

**Output**:
```
NAME       READY   STATUS    IP           NODE        NOMINATED NODE   READINESS GATES
nginx-1    1/1     Running   10.244.1.5   worker-01   <none>           <none>
nginx-2    1/1     Running   10.244.2.8   worker-02   <none>           <none>
```

**Additional columns explained**:

| Column | Meaning |
|--------|---------|
| `IP` | The pod's internal cluster IP address |
| `NODE` | The worker node running this pod |
| `NOMINATED NODE` | Node nominated for preemption (rare) |
| `READINESS GATES` | Custom readiness conditions |

---

#### `kubectl get pod -w`

📖 **What it does**: Watches pods in real-time, displaying updates as pod states change. The terminal stays open, streaming changes.

🎯 **When to use**: 
- Monitoring deployments in progress
- Watching pods scale up or down
- Debugging pods that keep restarting

**Syntax**:
```bash
kubectl get pod -w
```

**Example output** (changes appear as they happen):
```
NAME       READY   STATUS    RESTARTS   AGE
nginx-1    1/1     Running   0          5m
nginx-2    0/1     Pending   0          0s      # New line appears
nginx-2    0/1     ContainerCreating   0   2s  # Status updates
nginx-2    1/1     Running   0          5s      # Pod is ready
```

💡 **Pro tip**: Press `Ctrl+C` to stop watching.

💡 **Pro tip**: Combine with grep: `kubectl get pod -w | grep my-app`

---

#### `kubectl get pod -o yaml`

📖 **What it does**: Outputs the complete pod specification in YAML format, including all configuration details, status, and metadata.

🎯 **When to use**: 
- Understanding exactly how a pod is configured
- Creating templates for new pods
- Debugging configuration issues
- Exporting resources for version control

**Syntax**:
```bash
kubectl get pod <pod_name> -o yaml
```

**Example output** (abbreviated):
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-abc123
  namespace: default
  labels:
    app: nginx
  uid: 12345-67890-abcde
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
    resources:
      limits:
        cpu: "500m"
        memory: "128Mi"
status:
  phase: Running
  podIP: 10.244.1.5
  hostIP: 192.168.1.10
```

💡 **Pro tip**: Save to file for editing: `kubectl get pod nginx -o yaml > pod.yaml`

---

### Inspecting Pods

---

#### `kubectl describe pod <pod_name>` 🔥

📖 **What it does**: Shows comprehensive, human-readable information about a pod including events, conditions, container details, volumes, and more.

🎯 **When to use**: **This is your #1 debugging command!**
- Pod stuck in "Pending" → Check Events section
- Pod in "CrashLoopBackOff" → Check container exit codes
- Application not receiving traffic → Check conditions and probes

**Syntax**:
```bash
kubectl describe pod <pod_name>
```

**Output sections explained**:

```
Name:             nginx-deployment-abc123
Namespace:        default
Priority:         0
Service Account:  default
Node:             worker-01/192.168.1.10    ← Which node it's on
Start Time:       Mon, 15 Jan 2024 10:30:00 +0000
Labels:           app=nginx                  ← Labels for selection
                  pod-template-hash=abc123
Annotations:      <none>
Status:           Running
IP:               10.244.1.5                 ← Pod's IP address
```

```
Containers:
  nginx:
    Container ID:   containerd://abc123...
    Image:          nginx:1.21               ← Container image
    Image ID:       docker.io/library/nginx@sha256:...
    Port:           80/TCP
    State:          Running                  ← Container state
      Started:      Mon, 15 Jan 2024 10:30:05 +0000
    Ready:          True
    Restart Count:  0
    Limits:                                  ← Resource limits
      cpu:     500m
      memory:  128Mi
    Requests:                                ← Resource requests
      cpu:     100m
      memory:  64Mi
    Liveness:    http-get http://:80/ delay=10s timeout=1s period=10s
    Readiness:   http-get http://:80/ delay=5s timeout=1s period=5s
```

```
Conditions:                                  ← Pod conditions
  Type              Status
  Initialized       True                     ← Init containers done
  Ready             True                     ← Ready to serve traffic
  ContainersReady   True                     ← All containers ready
  PodScheduled      True                     ← Assigned to a node
```

```
Events:                                      ← LOOK HERE FOR PROBLEMS!
  Type     Reason     Age   From               Message
  ----     ------     ----  ----               -------
  Normal   Scheduled  10m   default-scheduler  Successfully assigned...
  Normal   Pulled     10m   kubelet            Container image already present
  Normal   Created    10m   kubelet            Created container nginx
  Normal   Started    10m   kubelet            Started container nginx
```

⚠️ **Critical**: Always check the **Events** section at the bottom. This is where Kubernetes tells you what went wrong!

**Common events indicating problems**:

| Event | Meaning |
|-------|---------|
| `FailedScheduling` | No node can run this pod |
| `FailedMount` | Volume mount failed |
| `ImagePullBackOff` | Cannot pull container image |
| `CrashLoopBackOff` | Container keeps crashing |
| `OOMKilled` | Container ran out of memory |

---

### Viewing Logs

---

#### `kubectl logs <pod_name>` 🔥

📖 **What it does**: Displays the stdout/stderr output from containers in the pod. This is your application's log output.

🎯 **When to use**: 
- Checking application errors or exceptions
- Debugging business logic issues
- Verifying application startup
- Monitoring application behavior

**Syntax**:
```bash
kubectl logs <pod_name>
```

**Output example**:
```
2024/01/15 10:30:05 [notice] 1#1: nginx/1.21.0
2024/01/15 10:30:05 [notice] 1#1: built by gcc 8.3.0
2024/01/15 10:30:05 [notice] 1#1: start worker processes
2024/01/15 10:32:15 10.244.2.1 - - "GET /health HTTP/1.1" 200 2
2024/01/15 10:32:45 10.244.2.1 - - "GET /api/users HTTP/1.1" 500 45
                                                             ↑
                                                    Error to investigate!
```

---

#### `kubectl logs -f <pod_name>`

📖 **What it does**: Follows/streams logs in real-time (like `tail -f`).

🎯 **When to use**: 
- Live monitoring of application output
- Watching for specific events
- Debugging issues as they happen

**Syntax**:
```bash
kubectl logs -f <pod_name>
```

💡 **Pro tip**: Press `Ctrl+C` to stop following.

---

#### `kubectl logs <pod_name> --previous`

📖 **What it does**: Shows logs from the previous container instance (before it crashed/restarted).

🎯 **When to use**: 
- Container keeps crashing and you need to see what happened
- Investigating why a restart occurred

**Syntax**:
```bash
kubectl logs <pod_name> --previous
```

⚠️ **Note**: Only works if the container has restarted at least once.

---

#### Additional Log Options

```bash
# Last 100 lines only
kubectl logs <pod_name> --tail=100

# Logs from the last hour
kubectl logs <pod_name> --since=1h

# Logs since a specific time
kubectl logs <pod_name> --since-time="2024-01-15T10:00:00Z"

# Logs from a specific container (multi-container pods)
kubectl logs <pod_name> -c <container_name>

# Logs from all containers in the pod
kubectl logs <pod_name> --all-containers=true

# Add timestamps to each log line
kubectl logs <pod_name> --timestamps=true
```

---

### Executing Commands in Pods

---

#### `kubectl exec -it <pod_name> -- /bin/bash` 🔥

📖 **What it does**: Opens an interactive shell session inside the running container, giving you direct access to the container's filesystem and environment.

🎯 **When to use**: 
- Debugging inside the container
- Checking files, configs, or environment variables
- Testing network connectivity from within the cluster
- Running diagnostic commands

**Syntax**:
```bash
kubectl exec -it <pod_name> -- /bin/bash
```

**Understanding the flags**:

| Flag | Meaning |
|------|---------|
| `-i` | Keep stdin open (interactive) |
| `-t` | Allocate a pseudo-TTY (terminal) |
| `--` | Separates kubectl arguments from the command |

**Example session**:
```bash
$ kubectl exec -it nginx-abc123 -- /bin/bash
root@nginx-abc123:/# ls -la
total 12
drwxr-xr-x 1 root root 4096 Jan 15 10:30 .
drwxr-xr-x 1 root root 4096 Jan 15 10:30 ..
drwxr-xr-x 2 root root 4096 Jan 15 10:30 etc
root@nginx-abc123:/# cat /etc/nginx/nginx.conf
# nginx configuration...
root@nginx-abc123:/# exit
$
```

💡 **Pro tip**: If `/bin/bash` isn't available, try `/bin/sh`:
```bash
kubectl exec -it <pod_name> -- /bin/sh
```

---

#### Non-Interactive Commands

📖 **What it does**: Runs a single command in the container and returns the output.

```bash
# Check environment variables
kubectl exec <pod_name> -- env

# Test network connectivity
kubectl exec <pod_name> -- curl -s http://other-service:8080/health

# Check filesystem
kubectl exec <pod_name> -- ls -la /app

# View a configuration file
kubectl exec <pod_name> -- cat /etc/nginx/nginx.conf

# Check running processes
kubectl exec <pod_name> -- ps aux

# Check network interfaces
kubectl exec <pod_name> -- ip addr

# Test DNS resolution
kubectl exec <pod_name> -- nslookup kubernetes.default
```

---

### Pod Lifecycle Management

---

#### `kubectl delete pod <pod_name>`

📖 **What it does**: Terminates and removes the specified pod from the cluster.

🎯 **When to use**: 
- Forcing a pod restart
- Cleaning up after testing
- Removing stuck or problematic pods

**Syntax**:
```bash
kubectl delete pod <pod_name>
```

**Important behaviors**:

| Scenario | What Happens |
|----------|--------------|
| Pod managed by Deployment | New pod created automatically |
| Standalone pod | Pod is permanently deleted |
| Pod with finalizers | Deletion waits for finalizers |

**Variations**:
```bash
# Delete multiple pods
kubectl delete pod pod1 pod2 pod3

# Delete pods by label
kubectl delete pod -l app=nginx

# Delete all pods in namespace
kubectl delete pod --all

# Force immediate deletion (use with caution!)
kubectl delete pod <pod_name> --grace-period=0 --force

# Delete with a custom grace period (seconds)
kubectl delete pod <pod_name> --grace-period=60
```

⚠️ **Warning**: `--force` bypasses graceful shutdown. Only use when absolutely necessary.

---

#### `kubectl edit pod <pod_name>`

📖 **What it does**: Opens the pod's YAML specification in your default text editor. Saving changes applies them to the cluster.

🎯 **When to use**: Quick modifications when you need to change something immediately.

**Syntax**:
```bash
kubectl edit pod <pod_name>
```

⚠️ **Important limitation**: Most pod fields are **immutable** after creation. You can only edit:
- `spec.containers[*].image`
- `spec.initContainers[*].image`  
- `spec.tolerations`
- Certain metadata fields

💡 **Better approach**: For most changes, edit the Deployment instead:
```bash
kubectl edit deployment <deployment_name>
```

---

## 4.3 Pod Selection and Filtering

### Using Labels

Labels are key-value pairs attached to objects. They're essential for organizing and selecting resources.

```bash
# Show labels in output
kubectl get pods --show-labels

# Filter by label
kubectl get pods -l app=nginx
kubectl get pods -l environment=production
kubectl get pods -l 'app in (nginx, apache)'
kubectl get pods -l app=nginx,version=v1

# Select pods WITHOUT a label
kubectl get pods -l '!environment'
```

### Using Field Selectors

```bash
# Filter by status
kubectl get pods --field-selector=status.phase=Running

# Filter by node
kubectl get pods --field-selector=spec.nodeName=worker-01

# Combine field selectors
kubectl get pods --field-selector=status.phase=Running,spec.nodeName=worker-01
```

---

# 📘 Chapter 5: Managing Nodes

<div align="center">
<i>"Your nodes are the foundation—keep them healthy, and your cluster thrives."</i>
</div>

<br>

## 5.1 Understanding Nodes

A **Node** is a worker machine in Kubernetes. It provides the compute resources where your pods actually run. Each node is managed by the control plane and contains the services necessary to run pods.

### Node Components

```
┌─────────────────────────────────────────────────────────────────┐
│                           NODE                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                      kubelet                             │   │
│   │  • Registers node with cluster                          │   │
│   │  • Watches for pod assignments                          │   │
│   │  • Reports node and pod status                          │   │
│   │  • Runs container health checks                         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                     kube-proxy                           │   │
│   │  • Maintains network rules                               │   │
│   │  • Enables Service abstraction                          │   │
│   │  • Handles pod-to-pod networking                        │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                  Container Runtime                       │   │
│   │  • Docker, containerd, or CRI-O                         │   │
│   │  • Pulls images and runs containers                     │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                  │
│   ┌───────────┐  ┌───────────┐  ┌───────────┐                  │
│   │    Pod    │  │    Pod    │  │    Pod    │                  │
│   │┌─────────┐│  │┌─────────┐│  │┌─────────┐│                  │
│   ││Container││  ││Container││  ││Container││                  │
│   │└─────────┘│  │└─────────┘│  │└─────────┘│                  │
│   └───────────┘  └───────────┘  └───────────┘                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## 5.2 Node Commands Reference

### Viewing Nodes

---

#### `kubectl get nodes`

📖 **What it does**: Lists all nodes in your cluster with their status, roles, age, and Kubernetes version.

🎯 **When to use**: 
- Checking cluster health at a glance
- Verifying all nodes are operational
- Checking Kubernetes versions across nodes

**Syntax**:
```bash
kubectl get nodes
kubectl get node      # Singular form works too
kubectl get no        # Short form
```

**Output**:
```
NAME          STATUS   ROLES           AGE   VERSION
master-01     Ready    control-plane   30d   v1.28.0
worker-01     Ready    <none>          30d   v1.28.0
worker-02     Ready    <none>          30d   v1.28.0
worker-03     NotReady <none>          30d   v1.28.0  ← Problem!
```

**Status values**:

| Status | Meaning |
|--------|---------|
| `Ready` | Node is healthy and accepting pods |
| `NotReady` | Node has a problem (check conditions) |
| `SchedulingDisabled` | Node is cordoned |

---

#### `kubectl describe node <node_name>` 🔥

📖 **What it does**: Shows comprehensive information about a node including capacity, allocatable resources, conditions, running pods, and events.

🎯 **When to use**: 
- Investigating why a node is NotReady
- Checking available resources
- Debugging scheduling failures
- Understanding node configuration

**Syntax**:
```bash
kubectl describe node <node_name>
```

**Key output sections**:

```yaml
# System Information
System Info:
  Machine ID:                 abc123...
  OS Image:                   Ubuntu 22.04 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  containerd://1.6.20
  Kubelet Version:           v1.28.0
  Kube-Proxy Version:        v1.28.0
```

```yaml
# Resource Capacity vs Allocatable
Capacity:
  cpu:                4           # Total CPU cores
  memory:             16Gi        # Total memory
  pods:               110         # Max pods on node
  
Allocatable:                      # Available for pods
  cpu:                3800m       # ~3.8 cores (some reserved)
  memory:             14Gi        # Some reserved for system
  pods:               110
```

```yaml
# Current Resource Usage
Allocated resources:
  CPU Requests:      2100m (55%)  # Requested by pods
  CPU Limits:        4000m (105%) # Could overcommit!
  Memory Requests:   8Gi (57%)
  Memory Limits:     12Gi (86%)
```

```yaml
# Node Conditions - CRITICAL FOR DEBUGGING
Conditions:
  Type                 Status    Reason
  ----                 ------    ------
  MemoryPressure       False     KubeletHasSufficientMemory
  DiskPressure         False     KubeletHasNoDiskPressure
  PIDPressure          False     KubeletHasSufficientPID
  Ready                True      KubeletReady           ← Should be True!
```

**Conditions explained**:

| Condition | True Means | False Means |
|-----------|------------|-------------|
| `Ready` | Node is healthy | Node has a problem |
| `MemoryPressure` | Low memory! | Memory OK |
| `DiskPressure` | Low disk space! | Disk OK |
| `PIDPressure` | Too many processes! | PIDs OK |
| `NetworkUnavailable` | Network not configured | Network OK |

---

#### `kubectl get node <node_name> -o yaml`

📖 **What it does**: Outputs the complete node specification in YAML format.

🎯 **When to use**: 
- Inspecting node labels and taints
- Exporting node configuration
- Understanding node annotations

**Syntax**:
```bash
kubectl get node <node_name> -o yaml
```

---

### Node Resource Monitoring

---

#### `kubectl top node` 🔥

📖 **What it does**: Shows real-time CPU and memory utilization for each node.

🎯 **When to use**: 
- Identifying resource-constrained nodes
- Capacity planning
- Investigating performance issues

**Syntax**:
```bash
kubectl top node
```

**Output**:
```
NAME        CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
master-01   256m         6%     2847Mi          18%
worker-01   1892m        47%    12043Mi         77%    ← High usage!
worker-02   523m         13%    4521Mi          29%
```

⚠️ **Prerequisite**: Requires metrics-server installed in your cluster.

```bash
# Install metrics-server if needed
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

---

### Node Maintenance Operations

---

#### `kubectl cordon <node_name>`

📖 **What it does**: Marks a node as **unschedulable**. Existing pods continue running, but no new pods will be placed on this node.

🎯 **When to use**: 
- Preparing for maintenance without disrupting current workloads
- Investigating node issues
- Gradual workload migration

**Syntax**:
```bash
kubectl cordon <node_name>
```

**What happens**:
```
Before: kubectl get nodes
NAME        STATUS   ROLES    AGE   VERSION
worker-01   Ready    <none>   30d   v1.28.0

After: kubectl cordon worker-01
NAME        STATUS                     ROLES    AGE   VERSION
worker-01   Ready,SchedulingDisabled   <none>   30d   v1.28.0
            ↑
            Node is cordoned
```

💡 **Note**: Existing pods keep running! This only prevents NEW pods.

---

#### `kubectl uncordon <node_name>`

📖 **What it does**: Removes the unschedulable mark, allowing new pods to be scheduled on the node again.

🎯 **When to use**: 
- After maintenance is complete
- Bringing a node back into the scheduling rotation

**Syntax**:
```bash
kubectl uncordon <node_name>
```

---

#### `kubectl drain <node_name>` 🔥

📖 **What it does**: Safely evicts all pods from a node. The node is cordoned (marked unschedulable), and pods are gracefully terminated and rescheduled to other nodes.

🎯 **When to use**: 
- Before node maintenance (OS updates, hardware repairs)
- Before removing a node from the cluster
- Upgrading Kubernetes on nodes

**Syntax**:
```bash
kubectl drain <node_name>
```

**What happens**:
1. Node is marked unschedulable (cordoned)
2. Each pod receives a termination signal
3. Pods are given time to shut down gracefully
4. Pods are rescheduled to other nodes
5. Node is empty except for DaemonSets

**Common issues and solutions**:

```bash
# Issue: DaemonSet pods block drain
# Solution: Ignore them (they're node-specific anyway)
kubectl drain <node_name> --ignore-daemonsets

# Issue: Pods with emptyDir volumes
# Solution: Allow deletion (data will be lost)
kubectl drain <node_name> --delete-emptydir-data

# Issue: Standalone pods (not managed by controller)
# Solution: Force deletion
kubectl drain <node_name> --force

# Complete command for maintenance:
kubectl drain <node_name> \
  --ignore-daemonsets \
  --delete-emptydir-data \
  --force
```

⚠️ **Warning**: `--force` deletes standalone pods permanently!

---

### Complete Node Maintenance Workflow

```bash
# Step 1: Check current state
kubectl get nodes
kubectl top node

# Step 2: Cordon the node (stop new pods)
kubectl cordon worker-01
echo "✓ Node cordoned - no new pods will be scheduled"

# Step 3: Drain existing pods
kubectl drain worker-01 \
  --ignore-daemonsets \
  --delete-emptydir-data
echo "✓ All pods evicted"

# Step 4: Verify node is empty
kubectl get pods -o wide | grep worker-01
echo "✓ No pods running on worker-01"

# ═══════════════════════════════════════════
# Step 5: PERFORM YOUR MAINTENANCE
# - OS updates: apt upgrade / yum update
# - Kernel updates
# - Hardware maintenance
# - Kubernetes upgrades
# ═══════════════════════════════════════════

# Step 6: Uncordon the node
kubectl uncordon worker-01
echo "✓ Node back in rotation"

# Step 7: Verify node is Ready
kubectl get node worker-01
echo "✓ Maintenance complete!"
```

---

# 📘 Chapter 6: Deployments & ReplicaSets

<div align="center">
<i>"Deployments are the heart of Kubernetes—they keep your applications alive and evolving."</i>
</div>

<br>

## 6.1 Understanding Deployments

A **Deployment** provides declarative updates for Pods and ReplicaSets. It's the recommended way to manage stateless applications in Kubernetes.

### The Deployment Hierarchy

```
                    ┌─────────────────────────────────┐
                    │          DEPLOYMENT             │
                    │  • Desired state declaration    │
                    │  • Rolling update strategy      │
                    │  • Rollback capabilities        │
                    └───────────────┬─────────────────┘
                                    │
                                    │ manages
                                    ▼
                    ┌─────────────────────────────────┐
                    │          REPLICASET             │
                    │  • Maintains desired replicas   │
                    │  • Creates/deletes pods         │
                    │  • Tracks pod template version  │
                    └───────────────┬─────────────────┘
                                    │
                                    │ manages
                                    ▼
              ┌─────────────────────┼─────────────────────┐
              │                     │                     │
        ┌─────┴─────┐         ┌─────┴─────┐         ┌─────┴─────┐
        │    Pod    │         │    Pod    │         │    Pod    │
        │  replica  │         │  replica  │         │  replica  │
        └───────────┘         └───────────┘         └───────────┘
```

### Why Use Deployments?

| Feature | Without Deployment | With Deployment |
|---------|-------------------|-----------------|
| Pod fails | Manual intervention | Auto-replaced |
| Update image | Delete and recreate | Rolling update |
| Bad update | Manual rollback | `kubectl rollout undo` |
| Scale | Manual | `kubectl scale` |

## 6.2 Deployment Commands Reference

### Viewing Deployments

---

#### `kubectl get deployment`

📖 **What it does**: Lists all deployments showing their name, ready replicas, up-to-date replicas, available replicas, and age.

🎯 **When to use**: 
- Checking application deployment status
- Verifying all replicas are ready
- Quick health check of applications

**Syntax**:
```bash
kubectl get deployment
kubectl get deployments   # Plural works
kubectl get deploy        # Short form
```

**Output**:
```
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
nginx      3/3     3            3           5d
api        2/3     3            2           1h    ← 1 replica not ready
frontend   5/5     5            5           10d
```

**Understanding the columns**:

| Column | Meaning |
|--------|---------|
| `READY` | Ready replicas / Desired replicas |
| `UP-TO-DATE` | Replicas with latest pod template |
| `AVAILABLE` | Replicas available for traffic |
| `AGE` | Time since deployment creation |

---

#### `kubectl get deployment <name> -o wide`

📖 **What it does**: Shows additional information including containers, images, and selectors.

**Output**:
```
NAME    READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES        SELECTOR
nginx   3/3     3            3           5d    nginx        nginx:1.21    app=nginx
```

---

#### `kubectl describe deployment <name>` 🔥

📖 **What it does**: Shows comprehensive deployment information including strategy, conditions, replica status, and events.

🎯 **When to use**: 
- Understanding deployment configuration
- Debugging rollout issues
- Checking update strategy settings

**Syntax**:
```bash
kubectl describe deployment <deployment_name>
```

**Key output sections**:

```yaml
Name:                   nginx-deployment
Namespace:              default
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable

StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
                        ↑
                        Controls how updates happen
```

```yaml
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
```

```yaml
Events:
  Type    Reason             Age   Message
  ----    ------             ----  -------
  Normal  ScalingReplicaSet  5m    Scaled up replica set nginx-abc to 3
```

---

### Managing Deployments

---

#### `kubectl scale deployment <name> --replicas=<n>` 🔥

📖 **What it does**: Changes the number of pod replicas for a deployment. Kubernetes will create or terminate pods to match the desired count.

🎯 **When to use**: 
- Scaling up for increased traffic
- Scaling down to save resources
- Scaling to zero to pause an application

**Syntax**:
```bash
kubectl scale deployment <deployment_name> --replicas=<number>
```

**Examples**:
```bash
# Scale up for more capacity
kubectl scale deployment api --replicas=10

# Scale down to save resources
kubectl scale deployment api --replicas=2

# Scale to zero (pause application, keeps config)
kubectl scale deployment api --replicas=0

# Scale multiple deployments at once
kubectl scale deployment api worker frontend --replicas=3
```

**What happens when you scale**:

```
Before: 2 replicas
┌─────┐ ┌─────┐
│ Pod │ │ Pod │
└─────┘ └─────┘

kubectl scale deployment nginx --replicas=5

After: 5 replicas
┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐
│ Pod │ │ Pod │ │ Pod │ │ Pod │ │ Pod │
└─────┘ └─────┘ └─────┘ └─────┘ └─────┘
                ↑       ↑       ↑
              New pods created
```

---

#### `kubectl edit deployment <name>`

📖 **What it does**: Opens the deployment's YAML in your default editor. Saving changes triggers a rollout if the pod template changed.

🎯 **When to use**: 
- Quick configuration changes
- Updating environment variables
- Modifying resource limits

**Syntax**:
```bash
kubectl edit deployment <deployment_name>
```

💡 **Pro tip**: Set your preferred editor:
```bash
export KUBE_EDITOR="vim"      # or nano, code, etc.
```

---

#### `kubectl delete deployment <name>`

📖 **What it does**: Deletes the deployment AND all pods it manages.

🎯 **When to use**: 
- Removing an application from the cluster
- Cleaning up test deployments

**Syntax**:
```bash
kubectl delete deployment <deployment_name>
```

⚠️ **Warning**: This is destructive! All pods will be terminated.

**Variations**:
```bash
# Delete from YAML file
kubectl delete -f deployment.yaml

# Delete but keep pods running (orphan them)
kubectl delete deployment nginx --cascade=orphan
```

---

# 📘 Chapter 7: Services & Networking

<div align="center">
<i>"Services are the glue that connects your applications to each other and to the world."</i>
</div>

<br>

## 7.1 Understanding Services

A **Service** is an abstraction that defines a logical set of pods and a policy to access them. Services enable loose coupling between dependent pods.

### The Problem Services Solve

```
Without Services:
┌─────────────────────────────────────────────────────────────┐
│  Client needs to know pod IPs, but pods are ephemeral!      │
│                                                             │
│  Pod A (10.1.1.5) dies → Pod A' (10.1.1.99) replaces it    │
│                                                             │
│  Client: "Where did my backend go?!" 😱                     │
└─────────────────────────────────────────────────────────────┘

With Services:
┌─────────────────────────────────────────────────────────────┐
│  Client connects to Service (stable IP/DNS), always works!  │
│                                                              │
│  Client → Service (10.96.0.50) → Pod A' (10.1.1.99)        │
│                    "backend"                                 │
│                                                              │
│  Client: "Works every time!" 😊                             │
└─────────────────────────────────────────────────────────────┘
```

### Service Types Explained

```
                        INTERNET
                            │
                            ▼
┌───────────────────────────────────────────────────────────────┐
│                                                               │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │              LoadBalancer Service                        │ │
│  │  • Gets external IP from cloud provider                 │ │
│  │  • Routes external traffic to NodePort                  │ │
│  │  • Example: AWS ELB, GCP LB, Azure LB                   │ │
│  └─────────────────────────────────────────────────────────┘ │
│                            │                                  │
│                            ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │              NodePort Service                            │ │
│  │  • Opens a port (30000-32767) on every node            │ │
│  │  • External access via NodeIP:NodePort                  │ │
│  │  • Routes to ClusterIP                                  │ │
│  └─────────────────────────────────────────────────────────┘ │
│                            │                                  │
│                            ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │              ClusterIP Service (Default)                 │ │
│  │  • Internal cluster IP only                             │ │
│  │  • DNS name: <service>.<namespace>.svc.cluster.local    │ │
│  │  • Load balances to backend pods                        │ │
│  └─────────────────────────────────────────────────────────┘ │
│                            │                                  │
│                            ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                      PODS                                │ │
│  │     ┌─────┐       ┌─────┐       ┌─────┐                │ │
│  │     │ Pod │       │ Pod │       │ Pod │                │ │
│  │     └─────┘       └─────┘       └─────┘                │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

| Type | External Access | Use Case |
|------|-----------------|----------|
| **ClusterIP** | No (internal only) | Service-to-service within cluster |
| **NodePort** | Yes (via Node IP) | Development, on-prem without LB |
| **LoadBalancer** | Yes (dedicated IP) | Production in cloud |
| **ExternalName** | N/A (DNS alias) | Reference external services |

## 7.2 Service Commands Reference

### Viewing Services

---

#### `kubectl get svc`

📖 **What it does**: Lists all services showing their type, cluster IP, external IP, ports, and age.

🎯 **When to use**: 
- Checking what services exist
- Finding service IPs and ports
- Verifying external IPs for LoadBalancers

**Syntax**:
```bash
kubectl get svc
kubectl get service     # Full form
kubectl get services    # Plural works too
```

**Output**:
```
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP    PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1       <none>         443/TCP          30d
api          LoadBalancer   10.96.45.123    34.56.78.90    80:31234/TCP     5d
web          NodePort       10.96.78.45     <none>         80:30080/TCP     2d
redis        ClusterIP      10.96.12.34     <none>         6379/TCP         10d
```

**Understanding the output**:

| Column | Description |
|--------|-------------|
| `CLUSTER-IP` | Internal IP (always assigned) |
| `EXTERNAL-IP` | Public IP (LoadBalancer only) |
| `PORT(S)` | ServicePort:NodePort/Protocol |

---

#### `kubectl describe svc <name>` 🔥

📖 **What it does**: Shows detailed service information including endpoints (the pod IPs it routes to).

🎯 **When to use**: 
- Debugging connectivity issues
- Verifying service targets the right pods
- Checking if endpoints are populated

**Syntax**:
```bash
kubectl describe svc <service_name>
```

**Key output sections**:

```yaml
Name:                     api
Namespace:                default
Labels:                   app=api
Selector:                 app=api          ← Which pods to target
Type:                     LoadBalancer
IP:                       10.96.45.123     ← Cluster IP
LoadBalancer Ingress:     34.56.78.90      ← External IP

Port:                     http  80/TCP     ← Service port
TargetPort:               8080/TCP         ← Container port
NodePort:                 http  31234/TCP  ← Node port

Endpoints:                10.244.1.5:8080,10.244.2.8:8080
                          ↑
                          Pod IPs receiving traffic
```

🔥 **Critical debugging tip**: If `Endpoints` is `<none>`, no pods match the service selector!

---

#### `kubectl get endpoints <name>`

📖 **What it does**: Shows the actual pod IP:port combinations a service routes to.

🎯 **When to use**: 
- Verifying pods are registered with the service
- Debugging "service not working" issues

**Syntax**:
```bash
kubectl get endpoints <service_name>
kubectl get ep <service_name>    # Short form
```

**Output**:
```
NAME   ENDPOINTS                                      AGE
api    10.244.1.5:8080,10.244.2.8:8080,10.244.3.2:8080   5d
```

**Troubleshooting with endpoints**:

| Endpoints | Meaning | Action |
|-----------|---------|--------|
| Multiple IPs | Healthy, multiple pods | ✅ Good |
| Single IP | Only one pod | Check replica count |
| `<none>` | No matching pods! | Check pod labels vs service selector |

---

### Service Troubleshooting Workflow

```bash
# Step 1: Check the service exists
kubectl get svc my-service

# Step 2: Check endpoints (are pods registered?)
kubectl get endpoints my-service
# If empty, the selector doesn't match any pods!

# Step 3: Check the selector
kubectl describe svc my-service | grep Selector
# Selector: app=my-app

# Step 4: Check pods have matching labels
kubectl get pods --show-labels | grep my-app
# If no pods have the label, that's the problem!

# Step 5: If pods exist, check they're Ready
kubectl get pods -l app=my-app
# STATUS should be Running, READY should be 1/1
```

---

# 📘 Chapter 8: ConfigMaps & Secrets

<div align="center">
<i>"Separate your configuration from your code—it's the cloud-native way."</i>
</div>

<br>

## 8.1 Understanding ConfigMaps

A **ConfigMap** is an API object that stores non-sensitive configuration data as key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or configuration files.

### Why Use ConfigMaps?

| Without ConfigMaps | With ConfigMaps |
|-------------------|-----------------|
| Config baked into image | Config separate from image |
| Rebuild image to change config | Change config without rebuild |
| Same config in all environments | Different configs per environment |

## 8.2 ConfigMap Commands

---

#### Creating ConfigMaps

```bash
# From literal key-value pairs
kubectl create configmap app-config \
  --from-literal=DATABASE_HOST=mysql \
  --from-literal=DATABASE_PORT=3306 \
  --from-literal=LOG_LEVEL=info
```

📖 **What it does**: Creates a ConfigMap with key-value pairs specified on the command line.

🎯 **When to use**: Simple configurations with a few values.

---

```bash
# From a file (filename becomes the key)
kubectl create configmap nginx-config --from-file=nginx.conf
```

📖 **What it does**: Creates a ConfigMap where the filename is the key and file contents are the value.

🎯 **When to use**: Storing configuration files (nginx.conf, app.properties, etc.).

---

```bash
# From an env file (KEY=VALUE format)
kubectl create configmap app-config --from-env-file=app.env
```

📖 **What it does**: Parses a `.env` file and creates keys from each line.

🎯 **When to use**: When you have configuration in `.env` file format.

---

#### Viewing ConfigMaps

```bash
# List all ConfigMaps
kubectl get configmap
kubectl get cm          # Short form

# View ConfigMap contents
kubectl get configmap <name> -o yaml

# Describe ConfigMap
kubectl describe configmap <name>
```

---

## 8.3 Understanding Secrets

A **Secret** is similar to a ConfigMap but designed for sensitive data. Secrets are base64-encoded (not encrypted by default).

### Secret Types

| Type | Use Case |
|------|----------|
| `generic` | Arbitrary key-value pairs |
| `docker-registry` | Container registry credentials |
| `tls` | TLS certificates |

## 8.4 Secret Commands

---

#### Creating Secrets

```bash
# From literal values
kubectl create secret generic db-creds \
  --from-literal=username=admin \
  --from-literal=password=super-secret-123
```

📖 **What it does**: Creates a Secret with key-value pairs. Values are automatically base64-encoded.

⚠️ **Security note**: The values are visible in your shell history!

---

```bash
# From files
kubectl create secret generic ssh-key \
  --from-file=id_rsa=/path/to/private-key

# TLS certificate
kubectl create secret tls my-tls-secret \
  --cert=path/to/tls.crt \
  --key=path/to/tls.key
```

---

#### Viewing Secrets

```bash
# List secrets
kubectl get secrets

# View secret metadata (not values)
kubectl describe secret <name>

# View encoded values
kubectl get secret <name> -o yaml

# Decode a specific value
kubectl get secret db-creds -o jsonpath='{.data.password}' | base64 -d
```

⚠️ **Warning**: Be careful with decoded secrets—don't expose them in logs or outputs!

---

# 📘 Chapter 9: Resource Creation Patterns

<div align="center">
<i>"There's a right way to create resources, and then there's the way that will haunt you at 3 AM."</i>
</div>

<br>

## 9.1 Imperative vs Declarative

### Imperative Commands

Direct commands that immediately perform actions.

```bash
# Create resources directly
kubectl run nginx --image=nginx
kubectl create deployment api --image=api:v1
kubectl expose deployment api --port=80
```

**Pros**: Quick, good for learning and testing
**Cons**: Not repeatable, no version control, easy to forget what you did

### Declarative Configuration

Define desired state in YAML files and let Kubernetes figure out how to achieve it.

```bash
kubectl apply -f deployment.yaml
```

**Pros**: Version controlled, repeatable, auditable, GitOps-friendly
**Cons**: More upfront effort

💡 **Best Practice**: Use imperative for learning/testing, declarative for production.

## 9.2 Generating YAML Templates

The `--dry-run=client -o yaml` pattern is incredibly useful for generating templates.

---

```bash
kubectl run nginx --image=nginx:1.21 \
  --dry-run=client -o yaml > pod.yaml
```

📖 **What it does**: Generates YAML without creating the resource.

**Workflow**:
1. Generate template with dry-run
2. Edit the YAML to add labels, resources, probes, etc.
3. Apply with `kubectl apply -f`

---

**Common templates**:

```bash
# Pod
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml

# Deployment
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > deployment.yaml

# Service (ClusterIP)
kubectl create service clusterip nginx --tcp=80:80 --dry-run=client -o yaml > service.yaml

# Service (NodePort)
kubectl create service nodeport nginx --tcp=80:80 --dry-run=client -o yaml > service-nodeport.yaml

# Job
kubectl create job backup --image=backup:v1 --dry-run=client -o yaml > job.yaml

# CronJob
kubectl create cronjob daily-backup --image=backup:v1 --schedule="0 2 * * *" --dry-run=client -o yaml > cronjob.yaml
```

## 9.3 Applying Resources

---

#### `kubectl apply -f <file>` 🔥

📖 **What it does**: Creates or updates resources defined in a YAML file. Kubernetes tracks the configuration and applies only what changed.

**Syntax**:
```bash
# Apply single file
kubectl apply -f deployment.yaml

# Apply multiple files
kubectl apply -f deployment.yaml -f service.yaml

# Apply all files in directory
kubectl apply -f ./kubernetes/

# Apply recursively
kubectl apply -f ./kubernetes/ -R

# Apply from URL
kubectl apply -f https://raw.githubusercontent.com/example/repo/main/manifest.yaml
```

---

#### Preview Changes Before Applying

```bash
# Server-side dry run (validates with API server)
kubectl apply -f deployment.yaml --dry-run=server

# Show differences
kubectl diff -f deployment.yaml
```

---

<div align="center">

# Part IV
# Advanced Operations

*"Mastery comes from understanding not just what, but why and when."*

</div>

---

# 📘 Chapter 10: Rollouts & Version Control

<div align="center">
<i>"The ability to update and rollback is what makes Kubernetes deployments production-ready."</i>
</div>

<br>

## 10.1 Understanding Rollouts

When you update a Deployment (like changing the container image), Kubernetes performs a **rolling update**:

```
Rolling Update Process:

Time 0: All pods running v1
┌────┐ ┌────┐ ┌────┐
│ v1 │ │ v1 │ │ v1 │
└────┘ └────┘ └────┘

Time 1: New v2 pod starting, one v1 terminating
┌────┐ ┌────┐ ┌────┐ ┌────┐
│ v1 │ │ v1 │ │ v1 │ │ v2 │
└────┘ └────┘ │term│ │new │
              └────┘ └────┘

Time 2: More v2 pods, fewer v1
┌────┐ ┌────┐ ┌────┐ ┌────┐
│ v1 │ │ v2 │ │ v2 │ │ v2 │
│term│ └────┘ └────┘ └────┘
└────┘

Time 3: All pods running v2
┌────┐ ┌────┐ ┌────┐
│ v2 │ │ v2 │ │ v2 │
└────┘ └────┘ └────┘
```

## 10.2 Rollout Commands Reference

---

#### `kubectl rollout status deployment <name>`

📖 **What it does**: Shows the current status of a rollout in progress.

🎯 **When to use**: After updating a deployment to verify it completes successfully.

**Syntax**:
```bash
kubectl rollout status deployment <deployment_name>
```

**Output**:
```
Waiting for deployment "api" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "api" rollout to finish: 2 of 3 updated replicas are available...
deployment "api" successfully rolled out
```

💡 **Pro tip**: Use in CI/CD pipelines with `--timeout`:
```bash
kubectl rollout status deployment api --timeout=5m
```

---

#### `kubectl rollout restart deployment <name>`

📖 **What it does**: Triggers a rolling restart of all pods without changing the configuration.

🎯 **When to use**: 
- After updating ConfigMaps or Secrets
- To clear application caches
- To pick up new images (with `imagePullPolicy: Always`)

**Syntax**:
```bash
kubectl rollout restart deployment <deployment_name>
```

---

#### `kubectl rollout undo deployment <name>` 🔥

📖 **What it does**: Rolls back to the previous version. This is your emergency rollback command!

🎯 **When to use**: When a new deployment is broken and you need to revert immediately.

**Syntax**:
```bash
kubectl rollout undo deployment <deployment_name>
```

**Example scenario**:
```bash
# Deploy new version
kubectl set image deployment/api api=api:v2

# Users report errors! Rollback immediately
kubectl rollout undo deployment api

# Verify rollback
kubectl rollout status deployment api
```

---

#### `kubectl rollout undo deployment <name> --to-revision=<n>`

📖 **What it does**: Rolls back to a specific revision number.

🎯 **When to use**: When you need to go back multiple versions.

**Syntax**:
```bash
kubectl rollout undo deployment <name> --to-revision=<revision_number>
```

---

#### `kubectl rollout history deployment <name>`

📖 **What it does**: Shows the revision history of a deployment.

**Syntax**:
```bash
kubectl rollout history deployment <deployment_name>
```

**Output**:
```
deployment.apps/api
REVISION  CHANGE-CAUSE
1         Initial deployment
2         kubectl set image deployment/api api=api:v2
3         kubectl set image deployment/api api=api:v3
```

💡 **Pro tip**: Add meaningful change causes:
```bash
kubectl annotate deployment api kubernetes.io/change-cause="Upgrade to v2 - adds caching"
```

---

#### `kubectl rollout history deployment <name> --revision=<n>`

📖 **What it does**: Shows details of a specific revision.

**Output**:
```yaml
deployment.apps/api with revision #2
Pod Template:
  Labels:       app=api
  Containers:
   api:
    Image:      api:v2
    Port:       8080/TCP
    Environment:
      DB_HOST:  mysql
```

---

#### `kubectl rollout pause/resume deployment <name>`

📖 **What it does**: Pauses or resumes a rollout in progress.

🎯 **When to use**: 
- Canary deployments: pause after partial rollout to test
- Spotted an issue: pause to investigate

**Syntax**:
```bash
kubectl rollout pause deployment <name>
kubectl rollout resume deployment <name>
```

---

# 📘 Chapter 11: DaemonSets, Jobs & CronJobs

<div align="center">
<i>"Not all workloads are long-running services—Kubernetes has you covered."</i>
</div>

<br>

## 11.1 DaemonSets

A **DaemonSet** ensures that a copy of a pod runs on all (or selected) nodes.

### Use Cases

- Log collectors (Fluentd, Filebeat)
- Node monitoring (Prometheus Node Exporter)
- Network plugins (Calico, Cilium)
- Storage daemons

### DaemonSet Commands

```bash
# List DaemonSets
kubectl get daemonset
kubectl get ds           # Short form

# Describe DaemonSet
kubectl describe daemonset <name>

# Edit DaemonSet (triggers rolling update)
kubectl edit daemonset <name>
```

---

## 11.2 Jobs

A **Job** creates pods that run to completion.

### Job Commands

```bash
# List Jobs
kubectl get jobs

# Describe Job
kubectl describe job <name>

# View Job pod logs
kubectl logs job/<name>

# Delete Job
kubectl delete job <name>
```

---

## 11.3 CronJobs

A **CronJob** creates Jobs on a schedule.

### CronJob Commands

```bash
# List CronJobs
kubectl get cronjobs

# Create a CronJob
kubectl create cronjob daily-backup \
  --image=backup:v1 \
  --schedule="0 2 * * *"

# Manually trigger a CronJob
kubectl create job --from=cronjob/daily-backup manual-backup-001
```

---

# 📘 Chapter 12: Monitoring & Observability

<div align="center">
<i>"You can't fix what you can't see."</i>
</div>

<br>

## 12.1 Resource Monitoring

---

#### `kubectl top node`

📖 **What it does**: Shows real-time CPU and memory usage per node.

```bash
kubectl top node

# Output:
# NAME        CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
# worker-01   1892m        47%    12043Mi         77%
```

---

#### `kubectl top pod`

📖 **What it does**: Shows real-time CPU and memory usage per pod.

```bash
kubectl top pod

# With container breakdown
kubectl top pod --containers

# Sort by resource
kubectl top pod --sort-by=cpu
kubectl top pod --sort-by=memory
```

---

## 12.2 Cluster Events

---

#### `kubectl get events`

📖 **What it does**: Shows cluster events (scheduling, pulling images, failures, etc.).

```bash
# All events
kubectl get events

# Sorted by time
kubectl get events --sort-by='.lastTimestamp'

# Watch events in real-time
kubectl get events -w

# Events in specific namespace
kubectl get events -n kube-system
```

---

# 📘 Chapter 13: Troubleshooting Guide

<div align="center">
<i>"Every error is an opportunity to understand Kubernetes better."</i>
</div>

<br>

## 13.1 Pod Troubleshooting Matrix

| Status | Meaning | Debug Steps |
|--------|---------|-------------|
| `Pending` | Cannot be scheduled | `kubectl describe pod` → Events |
| `ContainerCreating` | Image pulling or volume mounting | `kubectl describe pod` → Events |
| `ImagePullBackOff` | Cannot pull image | Check image name, registry auth |
| `CrashLoopBackOff` | Container keeps crashing | `kubectl logs --previous` |
| `Error` | Container exited with error | `kubectl logs`, check exit code |
| `Terminating` | Being deleted | Wait or force delete |

## 13.2 The Debugging Workflow

```bash
# Step 1: Check pod status
kubectl get pod <name>

# Step 2: Describe for events and conditions
kubectl describe pod <name>
# Look at: Conditions, Events, Container State

# Step 3: Check logs
kubectl logs <name>
kubectl logs <name> --previous    # If crashed

# Step 4: Exec into container (if running)
kubectl exec -it <name> -- /bin/sh

# Step 5: Check service and endpoints
kubectl get endpoints <service>
kubectl describe svc <service>
```

## 13.3 Common Issues and Solutions

### "No nodes available"

```bash
# Check node status
kubectl get nodes
kubectl describe node <node>

# Check for taints
kubectl get nodes -o json | jq '.items[].spec.taints'

# Check resource capacity
kubectl describe node <node> | grep -A 10 "Allocated resources"
```

### "ImagePullBackOff"

```bash
# Check image name spelling
kubectl describe pod <name> | grep Image

# Check registry credentials
kubectl get secret regcred -o yaml

# Manually test pull
docker pull <image>
```

### "CrashLoopBackOff"

```bash
# Check previous logs
kubectl logs <pod> --previous

# Check exit code
kubectl describe pod <pod> | grep -A 5 "State:"

# Check resource limits
kubectl describe pod <pod> | grep -A 10 "Limits:"
```

---

<div align="center">

# Appendices

</div>

---

# 📎 Appendix A: Quick Reference Card

## Most Used Commands

| Command | Description |
|---------|-------------|
| `kubectl get pods` | List pods |
| `kubectl get pods -o wide` | List pods with details |
| `kubectl describe pod <name>` | Pod details and events |
| `kubectl logs <pod>` | View pod logs |
| `kubectl logs -f <pod>` | Stream logs |
| `kubectl exec -it <pod> -- /bin/bash` | Shell into pod |
| `kubectl apply -f <file>` | Apply configuration |
| `kubectl delete -f <file>` | Delete resources |
| `kubectl scale deployment <name> --replicas=N` | Scale deployment |
| `kubectl rollout undo deployment <name>` | Rollback |

---

# 📎 Appendix B: Shell Aliases & Productivity

## Recommended Aliases

```bash
# Add to ~/.bashrc or ~/.zshrc

# Basic
alias k='kubectl'
alias kg='kubectl get'
alias kd='kubectl describe'
alias kdel='kubectl delete'
alias ka='kubectl apply -f'

# Pods
alias kgp='kubectl get pods'
alias kgpw='kubectl get pods -o wide'
alias kgpwatch='kubectl get pods -w'

# Deployments
alias kgd='kubectl get deployments'
alias ksd='kubectl scale deployment'

# Services
alias kgs='kubectl get services'

# Logs
alias kl='kubectl logs'
alias klf='kubectl logs -f'

# Exec
alias kx='kubectl exec -it'

# Context & Namespace
alias kctx='kubectl config use-context'
alias kns='kubectl config set-context --current --namespace'

# Useful functions
kexec() { kubectl exec -it "$1" -- /bin/sh; }
klogs() { kubectl logs -f $(kubectl get pods | grep "$1" | head -1 | awk '{print $1}'); }
```

---

# 📎 Appendix C: Glossary

| Term | Definition |
|------|------------|
| **Cluster** | A set of nodes running containerized applications |
| **Node** | A worker machine in Kubernetes |
| **Pod** | The smallest deployable unit; wraps one or more containers |
| **Deployment** | Manages ReplicaSets and provides rolling updates |
| **ReplicaSet** | Ensures a specified number of pod replicas are running |
| **Service** | An abstract way to expose pods as a network service |
| **Ingress** | Manages external HTTP/HTTPS access to services |
| **ConfigMap** | Stores non-sensitive configuration data |
| **Secret** | Stores sensitive data like passwords |
| **Namespace** | Virtual cluster for resource isolation |
| **Context** | Combination of cluster, user, and namespace |
| **Label** | Key-value pair for organizing resources |
| **Selector** | Query to select resources by labels |
| **kubelet** | Agent running on each node |
| **kube-proxy** | Network proxy on each node |

---

<div align="center">

---

## 📚 About This Guide

This guide is maintained by the Kubernetes community and is designed to help engineers at all levels master kubectl and Kubernetes operations.

### Contributing

We welcome contributions! Please submit issues and pull requests on GitHub.

### License

This work is licensed under the MIT License.

---

<p align="center">
<b>⭐ Star this repository if you found it helpful! ⭐</b>
</p>

<p align="center">
Made with ❤️ for the Cloud Native Community
</p>

<p align="center">
<i>First Edition • 2024</i>
</p>

</div>
