<!--
1.1_CI_CD_Core_Concepts.md
-->

<h1 align="center">🚀 Section 1.1 — Core CI/CD Concepts & Role of Jenkins</h1>

<hr>

<h2>🌍 Continuous Integration (CI)</h2>

<p><strong>Continuous Integration</strong> is a development practice — not just a tool. It's a philosophy focused on preventing problems that occur when multiple developers work in isolation and merge their changes infrequently.</p>

<h3>💥 The Problem It Solves:</h3>

<ul>
  <li><strong>"Merge Hell":</strong> Long-lived branches cause painful merge conflicts and debugging complexity when integrated late.</li>
  <li><strong>Broken Code:</strong> Without regular checks, bad code can be committed unnoticed, leading to delayed and difficult fixes.</li>
</ul>

<h3>⚙️ The CI Process (The Solution):</h3>

<ol>
  <li><strong>Commit Frequently:</strong> Developers push changes to a shared branch (like <code>main</code> or <code>develop</code>) daily.</li>
  <li><strong>Trigger Automated Build:</strong> Every push automatically starts a new build.</li>
  <li><strong>Run Automated Tests:</strong> Unit and integration tests run immediately after a successful build.</li>
  <li><strong>Provide Fast Feedback:</strong>
    <ul>
      <li><strong>If build/tests pass:</strong> Safe and stable integration confirmed.</li>
      <li><strong>If build/tests fail:</strong> The build is marked “broken” — the entire team is alerted, and fixing it becomes the top priority.</li>
    </ul>
  </li>
</ol>

<h3>🎯 Key Goals & Benefits:</h3>

<ul>
  <li><strong>Find Bugs Faster:</strong> Issues are caught minutes after being introduced.</li>
  <li><strong>Reduce Merge Conflicts:</strong> Frequent small merges are easier and safer.</li>
  <li><strong>Improve Code Quality:</strong> Automated tests maintain a consistent quality baseline.</li>
  <li><strong>Increase Developer Confidence:</strong> Safer experimentation and quicker iteration.</li>
</ul>

<hr>

<h2>🚚 Continuous Delivery (CD)</h2>

<p><strong>Continuous Delivery</strong> extends CI by automating the entire release process, keeping your application <em>always</em> in a deployable state.</p>

<h3>⚙️ The Process:</h3>

<ol>
  <li><strong>CI Passes:</strong> Code builds successfully and passes unit/integration tests.</li>
  <li><strong>Artifact Creation:</strong> The build produces a deployable artifact (e.g., <code>.jar</code>, Docker image, <code>.zip</code>), stored in Artifactory/Nexus/Docker Hub.</li>
  <li><strong>Automated Environment Promotion:</strong> The same artifact is deployed through QA → Staging environments, running automated end-to-end tests at each stage.</li>
  <li><strong>Manual Gate:</strong> Once all tests pass, a human approves production deployment — no automatic release.</li>
</ol>

<h3>🎯 Key Goals & Benefits:</h3>

<ul>
  <li><strong>Low-Risk Releases:</strong> Same tested artifact flows through all environments.</li>
  <li><strong>Release on Demand:</strong> Releases become business-driven, not technical.</li>
  <li><strong>Faster Feedback Loop:</strong> New features reach users or testers faster.</li>
</ul>

<hr>

<h2>🚀 Continuous Deployment (CD)</h2>

<p><strong>Continuous Deployment</strong> takes Continuous Delivery one step further — removing the final manual approval step.</p>

<h3>⚙️ The Process:</h3>

<ol>
  <li>Same pipeline as Continuous Delivery (CI → Build → Test → Deploy → Test).</li>
  <li><strong>No Manual Gate:</strong> After passing all automated checks, the system deploys directly to production.</li>
</ol>

<h3>🧩 Key Difference:</h3>

<ul>
  <li><strong>Continuous Delivery:</strong> Ready for deployment at any time.</li>
  <li><strong>Continuous Deployment:</strong> Automatically deployed after passing all stages.</li>
</ul>

<h3>🎯 Key Goals & Benefits:</h3>

<ul>
  <li><strong>Maximum Velocity:</strong> Code reaches production within minutes.</li>
  <li><strong>Smallest Possible Change:</strong> Each release is tiny, making rollback and debugging simple.</li>
  <li><strong>Requires High Trust:</strong> Needs strong automated tests and monitoring.</li>
</ul>

<hr>

<h2>🤖 What is Jenkins? (The Engine Behind CI/CD)</h2>

<p><strong>Jenkins</strong> is an open-source <strong>automation server</strong> that orchestrates your entire CI/CD pipeline — the "brain" that connects and automates all your tools.</p>

<h3>⚙️ Core Function:</h3>

<p>At its heart, Jenkins is a <strong>task orchestrator</strong> that automates repetitive development and deployment tasks.</p>

<h3>🧠 How Jenkins Works:</h3>

<ol>
  <li><strong>Listens for Triggers:</strong> Watches code repositories (like GitHub) for commits.</li>
  <li><strong>Runs Jobs or Pipelines:</strong> Executes a pre-defined set of build, test, and deploy steps.</li>
  <li><strong>Uses Plugins:</strong> Integrates with build tools, cloud services, and testing frameworks through a vast plugin ecosystem.</li>
</ol>

<h3>🧩 A Typical Jenkins CI/CD Flow:</h3>

<ol>
  <li>Developer pushes code to GitHub.</li>
  <li>GitHub sends a <strong>webhook</strong> to Jenkins.</li>
  <li>Jenkins triggers the pipeline (e.g., <code>My-App-Pipeline</code>).</li>
  <li>The pipeline performs the following steps:
    <ul>
      <li><strong>Step 1:</strong> Pull latest code using <strong>Git Plugin</strong>.</li>
      <li><strong>Step 2:</strong> Build using <strong>Maven Plugin</strong> (<code>mvn clean package</code>).</li>
      <li><strong>Step 3:</strong> Run unit tests using <strong>JUnit Plugin</strong>.</li>
      <li><strong>Step 4:</strong> Build Docker image with <strong>Docker Plugin</strong>.</li>
      <li><strong>Step 5:</strong> Retrieve credentials via <strong>Credentials Plugin</strong>.</li>
      <li><strong>Step 6:</strong> Push Docker image to Docker Hub.</li>
      <li><strong>Step 7:</strong> Deploy new image to Kubernetes using <strong>Kubernetes CLI Plugin</strong>.</li>
      <li><strong>Step 8:</strong> Send Slack notification: <em>"Build #142 successfully deployed to staging."</em></li>
    </ul>
  </li>
</ol>

<h3>💡 Summary:</h3>

<p>Jenkins is the central automation hub that connects source control, build tools, testing frameworks, container platforms, and deployment environments — enabling a smooth, reliable CI/CD workflow from code to production.</p>

<hr>

<h4 align="center">📘 End of Section 1.1 — Core CI/CD Concepts & Jenkins Overview</h4>


<hr/>
<hr/>
<h1 align="center">🧾 Jenkins Setup on Kubernetes (Kind Cluster)</h1>
<h3 align="center">Complete Step-by-Step Documentation</h3>

---

## 📘 1. Overview

This guide explains the complete procedure to **deploy Jenkins (Docker-in-Docker enabled)** on a **Kind (Kubernetes-in-Docker)** cluster using **WSL2 on Windows**.

It includes:

<ul>
  <li>Environment prerequisites</li>
  <li>Kind cluster setup</li>
  <li>Building a custom Jenkins image (Docker + Maven)</li>
  <li>Persistent storage setup</li>
  <li>Kubernetes manifests for Jenkins Deployment & Service</li>
  <li>Access, validation & troubleshooting</li>
</ul>

---

## 🧱 2. Prerequisites

### ✅ System Requirements

<table>
<tr><th>Component</th><th>Requirement</th></tr>
<tr><td>OS</td><td>Windows 10/11 with WSL2 enabled</td></tr>
<tr><td>WSL Distro</td><td>Ubuntu 20.04+ (recommended)</td></tr>
<tr><td>RAM</td><td>≥ 8 GB</td></tr>
<tr><td>CPU</td><td>≥ 2 cores</td></tr>
<tr><td>Disk</td><td>≥ 20 GB free</td></tr>
</table>

### ✅ Installed Tools

<table>
<tr><th>Tool</th><th>Check Command</th><th>Notes</th></tr>
<tr><td>Docker Desktop (WSL2 backend)</td><td><code>docker --version</code></td><td>Required for Kind</td></tr>
<tr><td>Kind</td><td><code>kind --version</code></td><td>Kubernetes-in-Docker tool</td></tr>
<tr><td>Kubectl</td><td><code>kubectl version --client</code></td><td>Kubernetes CLI</td></tr>
<tr><td>Git</td><td><code>git --version</code></td><td>Optional for Jenkins jobs</td></tr>
</table>

> ⚙️ **Note:** Ensure Docker Desktop is configured to use the **WSL2 backend** and exposes the Docker daemon to WSL.

---

## ⚙️ 3. Environment Setup

**Step 1:** Prepare a working directory

```bash
mkdir -p ~/kind-jenkins
cd ~/kind-jenkins
```

**Step 2:** Create directory for Jenkins data

```bash
sudo mkdir -p /mnt/data/jenkins
sudo chmod 777 /mnt/data/jenkins
```

> 💡 This directory will be used as a Persistent Volume (PV) inside the Kind cluster.

---

## 🧩 4. Kind Cluster Configuration

**File:** `kind-harbor-cluster.yaml`

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: demo
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30080  # Jenkins UI
        hostPort: 30080
        protocol: TCP
    extraMounts:
      - hostPath: /var/run/docker.sock
        containerPath: /var/run/docker.sock
      - hostPath: /mnt/data/jenkins
        containerPath: /mnt/data/jenkins
  - role: worker
    extraMounts:
      - hostPath: /var/run/docker.sock
        containerPath: /var/run/docker.sock
```

**Create cluster:**
```bash
kind create cluster --config kind-harbor-cluster.yaml
```

**Verify:**
```bash
kubectl get nodes
kubectl get pods -A
```

---

## 🐳 5. Custom Jenkins Docker Image (DIND)

**File:** `Dockerfile`

```dockerfile
FROM jenkins/jenkins:lts
USER root

RUN apt-get update && apt-get install -y \
    apt-transport-https ca-certificates curl gnupg lsb-release \
    git maven docker.io docker-compose \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

RUN usermod -aG docker jenkins

COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
```

**File:** `docker-entrypoint.sh`

```bash
#!/bin/bash
set -euo pipefail

dockerd > /var/log/dockerd.log 2>&1 &

for i in {1..30}; do
  if docker info > /dev/null 2>&1; then
    echo "Docker is ready"
    break
  fi
  echo "Waiting for Docker daemon to start..."
  sleep 1
done

exec /usr/bin/tini -- /usr/local/bin/jenkins.sh
```

**Build and load image:**

```bash
docker build -t praveen581348/jenkins-dind:23 .
kind load docker-image praveen581348/jenkins-dind:23 --name demo
```

---

## 📦 6. Kubernetes Manifests

**Namespace:**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: jenkins
```

**Persistent Volume & Claim:**
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: jenkins-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data/jenkins
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jenkins-pvc
  namespace: jenkins
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  volumeName: jenkins-pv
```

**Deployment:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins
  namespace: jenkins
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
        - name: jenkins
          image: praveen581348/jenkins-dind:23
          ports:
            - containerPort: 8080
          securityContext:
            privileged: true
          volumeMounts:
            - name: jenkins-home
              mountPath: /var/jenkins_home
            - name: docker-sock
              mountPath: /var/run/docker.sock
      volumes:
        - name: jenkins-home
          persistentVolumeClaim:
            claimName: jenkins-pvc
        - name: docker-sock
          hostPath:
            path: /var/run/docker.sock
```

**Service:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: jenkins
  namespace: jenkins
spec:
  type: NodePort
  ports:
    - port: 8080
      nodePort: 30080
  selector:
    app: jenkins
```

---

## 🚀 7. Deploy Jenkins

```bash
kubectl apply -f namespace.yaml
kubectl apply -f pv_pvc.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

**Check:**
```bash
kubectl get all -n jenkins
```

---

## 🌐 8. Access Jenkins UI

- If using **Docker Desktop**: [http://localhost:30080](http://localhost:30080)
- If using **pure WSL2**:
  ```bash
  hostname -I
  ```
  Then open:  
  👉 `http://<WSL-IP>:30080`

---

## 🔐 9. Retrieve Jenkins Initial Admin Password

```bash
kubectl exec -n jenkins deploy/jenkins -- cat /var/jenkins_home/secrets/initialAdminPassword
```

---

## ⚙️ 10. Post-Installation Setup

1. **Unlock Jenkins** → Paste the admin password.  
2. **Install Suggested Plugins**.  
3. **Create Admin User**.  
4. **Set Jenkins URL:** `http://localhost:30080`.  
5. **Verify Docker:** Run test Pipeline:

```groovy
pipeline {
  agent any
  stages {
    stage('Docker Test') {
      steps {
        sh 'docker version'
        sh 'docker run hello-world'
      }
    }
  }
}
```

---

## 🧠 11. Common Troubleshooting

<table>
<tr><th>Issue</th><th>Cause</th><th>Fix</th></tr>
<tr><td>Jenkins page not loading</td><td>Port not mapped</td><td>Ensure 30080 mapped in Kind YAML</td></tr>
<tr><td>PV pending</td><td>HostPath missing</td><td>Create <code>/mnt/data/jenkins</code> before cluster</td></tr>
<tr><td>Docker not found</td><td>Image not loaded into Kind</td><td>Run <code>kind load docker-image ...</code></td></tr>
<tr><td>CrashLoopBackOff</td><td>Permission or startup issue</td><td>Check <code>kubectl logs -n jenkins ...</code></td></tr>
<tr><td>Cannot access from Windows</td><td>WSL IP isolation</td><td>Use <code>http://&lt;WSL-IP&gt;:30080</code></td></tr>
</table>

---

## ✅ 12. Validation Commands

| Task | Command |
| :--- | :--- |
| Check cluster info | `kubectl cluster-info --context kind-demo` |
| List nodes | `kubectl get nodes -o wide` |
| List pods | `kubectl get pods -A` |
| Check Jenkins logs | `kubectl logs -n jenkins deploy/jenkins` |
| Check mapped ports | `docker ps` |
| Describe PV/PVC | `kubectl describe pv,pvc -n jenkins` |

---

## 🧰 13. Optional Enhancements

- Ingress Controller for subdomain access  
- Persistent Volume Backups  
- Jenkins Configuration as Code (JCasC)  
- GitHub Webhooks for CI  
- ArgoCD for GitOps-style management  
- Prometheus + Grafana for monitoring  

---

## 🧹 14. Cleanup

To delete the setup:
```bash
kind delete cluster --name demo
```

---

<h3 align="center">🏁 Final Notes</h3>

- Jenkins with Docker-in-Docker enables complete CI/CD pipelines.  
- Mapping ports (30080+) allows access from Windows & WSL.  
- Persistent volume ensures Jenkins data retention.  
- Always run `kind load docker-image` for custom images.  

---

<hr/>
<hr/>

