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

<!-- 🧾 Jenkins Freestyle Job Documentation -->

<h1 align="center">🚀 Jenkins Lab Notes: Phase 1.3 - SenderService Freestyle Pipeline</h1>

<p align="center">
  <b>Automating: Checkout → Maven Build → Docker Package</b><br>
  <i>Comprehensive step-by-step guide for the Sender Service CI process</i>
</p>

---

<h2>📝 Goal</h2>

<p>
To create a <b>Freestyle project</b> in Jenkins that automates the <b>Checkout → Build (Maven) → Package (Docker)</b> process for the <code>senderservice</code> Spring Boot application.
</p>

---

<h2>🔐 Step 1: Configure Docker Hub Credentials</h2>

<ol>
  <li>Go to <b>Manage Jenkins → Credentials</b>.</li>
  <li>Under <b>Stores scoped to Jenkins</b>, click <b>(Global)</b>.</li>
  <li>Click <b>Add Credentials</b>.</li>
  <li><b>Kind:</b> Username with password</li>
  <li><b>Username:</b> <code>praveen581348</code></li>
  <li><b>Password:</b> Docker Hub password or access token</li>
  <li><b>ID:</b> <code>dockerhub-creds</code></li>
  <li>Click <b>OK</b>.</li>
</ol>

<blockquote>
<b>💡 Study Note:</b> The <code>ID</code> is a unique label we’ll use in Jenkins jobs to reference these credentials safely.
</blockquote>

---

<h2>⚙️ Step 2: Job Creation & Configuration</h2>

<h3>2.1 ➤ Create New Item</h3>

<ol>
  <li>From the Jenkins dashboard, click <b>New Item</b>.</li>
  <li>Enter item name: <code>SenderService-Freestyle-Deploy</code></li>
  <li>Select <b>Freestyle project</b>.</li>
  <li>Click <b>OK</b>.</li>
</ol>

---

<h3>2.2 📋 General Section</h3>

<ul>
  <li><b>Description:</b> “Freestyle job to build, package, and deploy the senderservice Spring Boot application.”</li>
  <li><b>Discard Old Builds:</b> Check this box and set <b>Max # of builds to keep</b> = <code>20</code>.</li>
</ul>

<blockquote>
🧠 <b>Note:</b> This keeps Jenkins clean by removing old build logs and artifacts.
</blockquote>

---

<h3>2.3 📁 Source Code Management (SCM)</h3>

<ul>
  <li><b>SCM:</b> Select <b>Git</b>.</li>
  <li><b>Repository URL:</b> <code>https://github.com/praveen581348/senderservice.git</code></li>
  <li><b>Credentials:</b> None (public repo).</li>
  <li><b>Branches to build:</b> <code>*/main</code></li>
</ul>

---

<h3>2.4 ⏰ Build Triggers</h3>

<ul>
  <li><b>Option Used:</b> <code>Poll SCM</code></li>
  <li><b>Schedule:</b> <code>H/5 * * * *</code></li>
</ul>

<blockquote>
🔄 This means Jenkins checks for new commits every 5 minutes and builds automatically if it detects changes.
</blockquote>

---

<h3>2.5 🌳 Build Environment</h3>

<ul>
  <li><b>Delete workspace before build starts:</b> Ensures clean build environment.</li>
  <li><b>Use secret text(s) or file(s):</b> Provides Docker Hub credentials securely.</li>
</ul>

<h4>🧩 Credential Mapping:</h4>

<table border="1" cellpadding="4">
<tr><th>Variable</th><th>Value</th></tr>
<tr><td><code>DOCKER_USER</code></td><td>Docker Hub username</td></tr>
<tr><td><code>DOCKER_PASS</code></td><td>Docker Hub password (masked)</td></tr>
<tr><td><code>Credentials ID</code></td><td><code>dockerhub-creds</code></td></tr>
</table>

---

<h3>2.6 🏗️ Build Steps</h3>

<h4>Step 1: Maven Build</h4>

```bash
echo "--- 1. BUILDING MAVEN ARTIFACT ---"

# Use -DskipTests for speed; in production, run tests
mvn clean package -DskipTests

echo "Maven build complete. JAR is in /target directory."
```

<h4>Step 2: Docker Build</h4>

```bash
echo "--- 2. BUILDING DOCKER IMAGE ---"

# Unique tag for traceability
export IMAGE_TAG="praveen581348/senderservice:${BUILD_NUMBER}"

echo "Building image: $IMAGE_TAG"

docker build -t $IMAGE_TAG .

echo "Docker build complete."
```

---

<h2>🚀 Step 3: Running and Verifying the Build</h2>

<ol>
  <li>Click <b>Build Now</b> on job dashboard.</li>
  <li>Open <b>Console Output</b> from Build #1.</li>
</ol>

<h4>✅ Expected Output:</h4>

```bash
...
#1 [internal] load build definition from Dockerfile
#2 [internal] load metadata for docker.io/library/eclipse-temurin:17-jdk-jammy
...
#6 [2/2] COPY target/*.jar app.jar
#7 exporting to image
#7 naming to docker.io/praveen581348/senderservice:1
...
Finished: SUCCESS
```

---

<h2 align="center">🎯 Summary</h2>

<p align="center">
✅ Freestyle job created successfully<br>
✅ Automated Maven & Docker build pipeline<br>
✅ Credentials securely managed<br>
✅ Ready for next phase: <b>Docker Push + Helm Deploy</b>
</p>

---

<h3 align="center">📘 Author</h3>

<p align="center">
<b>Praveen</b> — Jenkins Freestyle Lab Documentation  
<small>Generated and verified with <code>Jenkins v2.x</code> and <code>Docker v24.x</code></small>
</p>
<hr/>

# 🚀 Jenkins Freestyle Job — The Annotated DevOps Guide

> **Purpose:**  
> This guide breaks down every section of a Jenkins Freestyle project configuration page — not just *what* to click, but *why* each option exists and when to actually use it.  
> Perfect for DevOps engineers learning Jenkins hands-on.

<hr>

<h2 style="color:#2d89ef;">1️⃣ General Section</h2>
<p>Defines the high-level behavior, metadata, and characteristics of your Jenkins job.</p>

<h3>🧩 Description</h3>
<ul>
<li><b>What:</b> A simple text box.</li>
<li><b>Why:</b> Use this to describe what the job does, who owns it, and its purpose.</li>
<li><b>Tip:</b> You can use <b>HTML</b> here.</li>
</ul>

<pre><code class="language-html">
<p><b>Service:</b> Sender Service</p>
<p><b>Purpose:</b> Builds & packages the Sender microservice Docker image</p>
<p><b>Owner:</b> DevOps Team</p>
</code></pre>

<h3>🧹 Discard Old Builds</h3>
<ul>
<li><b>Why:</b> Prevents Jenkins from filling its disk with logs and artifacts.</li>
<li><b>Recommended:</b> Always enable this option. Keep last <code>20</code> builds or <code>7</code> days.</li>
</ul>

<p><i>💡 Jenkins is not an artifact repository — use Nexus or Artifactory for long-term storage.</i></p>

<hr>

<h2 style="color:#00b050;">2️⃣ Source Code Management (SCM)</h2>
<p>Defines where the source code comes from.</p>

<h3>🧭 Git</h3>
<table>
<tr><th>Field</th><th>Description</th></tr>
<tr><td>Repository URL</td><td>HTTPS/SSH URL of the repo</td></tr>
<tr><td>Credentials</td><td>Select from stored Jenkins credentials</td></tr>
<tr><td>Branches to Build</td><td>Example: <code>*/main</code> or <code>*/feature-*</code></td></tr>
<tr><td>Repository Browser</td><td>Choose <code>githubweb</code> for clickable commit links</td></tr>
</table>

<p>You can also parameterize it using <code>${GIT_BRANCH}</code>.</p>

<hr>

<h2 style="color:#ff9900;">3️⃣ Build Triggers</h2>
<p>Defines <b>when</b> and <b>why</b> your job runs.</p>

<ul>
<li><b>Trigger builds remotely:</b> Generates a tokenized URL to trigger builds externally.</li>
<li><b>Build periodically:</b> Cron-based schedules. Example: <code>H 0 * * *</code></li>
<li><b>GitHub hook trigger:</b> Event-driven webhook for push notifications.</li>
<li><b>Poll SCM:</b> Polls repository at intervals (less efficient).</li>
</ul>

<hr>

<h2 style="color:#7030a0;">4️⃣ Build Environment</h2>
<p>Defines the context before build steps run.</p>

<ul>
<li><b>Delete workspace before build:</b> Ensures clean workspace each time.</li>
<li><b>Use secret text(s)/file(s):</b> Securely inject credentials like DockerHub login.</li>
<li><b>Add timestamps:</b> Useful for debugging build performance.</li>
<li><b>Terminate build if stuck:</b> Prevent infinite hanging jobs.</li>
</ul>

<pre><code class="language-bash">
docker login -u $DOCKER_USER -p $DOCKER_PASS
</code></pre>

<hr>

<h2 style="color:#c00000;">5️⃣ Build Steps</h2>
<p>The core actions your job executes.</p>

<ul>
<li><b>Execute Shell:</b> Generic shell script execution.</li>
<li><b>Invoke Maven Targets:</b> Integrated Maven execution with test parsing.</li>
</ul>

<pre><code class="language-bash">
echo "Building Sender Service..."
mvn clean package -DskipTests
docker build -t sender-service:latest .
</code></pre>

<hr>

<h2 style="color:#1f4e79;">6️⃣ Post-Build Actions</h2>
<p>Defines what happens after the build completes.</p>

<ul>
<li><b>Archive artifacts:</b> Preserve build outputs like <code>target/*.jar</code>.</li>
<li><b>Publish JUnit test results:</b> Parse and visualize <code>target/surefire-reports/*.xml</code>.</li>
<li><b>Build other projects:</b> Chain dependent jobs (e.g., deploy after build).</li>
<li><b>Email notifications:</b> Notify commit authors on failures.</li>
<li><b>Delete workspace:</b> Clean up after successful builds.</li>
</ul>

<hr>

<h2 style="color:#4bacc6;">✅ Best Practices Summary</h2>

<table>
<tr><th>Area</th><th>Recommendation</th></tr>
<tr><td>Cleanup</td><td>Always discard old builds</td></tr>
<tr><td>Triggers</td><td>Prefer GitHub Webhook over Poll SCM</td></tr>
<tr><td>Credentials</td><td>Inject securely using Jenkins credentials</td></tr>
<tr><td>Artifacts</td><td>Archive all critical files</td></tr>
<tr><td>Logs</td><td>Enable timestamps</td></tr>
<tr><td>Concurrency</td><td>Disable unless isolated</td></tr>
<tr><td>Timeout</td><td>Set reasonable limits</td></tr>
</table>

<hr>

<p><b>Author:</b> DevOps Jenkins Lab Notes<br>
<b>Version:</b> 1.0<br>
<b>License:</b> Internal Knowledgebase</p>
<hr/>
<hr/>

# 🧾 Jenkins Freestyle Job — Secure DockerHub Push (Two-Step Process)

<hr>

<h2>Step 1️⃣: Create Global Credentials</h2>

<ul>
<li>Navigate to <b>Manage Jenkins → Credentials</b>.</li>
<li>Add a new <b>Username with Password</b> credential.</li>
<li>Save it with <b>ID:</b> <code>dockerhub-creds</code>.</li>
<li>This securely stores your DockerHub login.</li>
</ul>

<hr>

<h2>Step 2️⃣: Bind Credentials to Environment Variables</h2>

<p>In your <b>SenderService-Freestyle-Deploy</b> job:</p>

<ol>
<li>Go to <b>Configure → Build Environment</b>.</li>
<li>Check ✅ <b>Use secret text(s) or file(s)</b>.</li>
<li>Click <b>Add → Username and password (separated)</b>.</li>
<li>Fill in the fields as shown below:</li>
</ol>

<table border="1" cellspacing="0" cellpadding="5">
<tr><th>Field</th><th>Value</th><th>Purpose</th></tr>
<tr><td><b>Credentials</b></td><td><code>praveen581348/dockerhub-creds</code></td><td>Uses stored DockerHub login</td></tr>
<tr><td><b>Username Variable</b></td><td><code>DOCKER_USER</code></td><td>Stores Docker username</td></tr>
<tr><td><b>Password Variable</b></td><td><code>DOCKER_PASS</code></td><td>Stores Docker password</td></tr>
</table>

<p>💡 Jenkins injects these as environment variables during the build — so <code>$DOCKER_USER</code> and <code>$DOCKER_PASS</code> become available in your shell steps.</p>

<hr>

<h2>🔧 Build Step 3: Push Docker Image</h2>

<pre><code class="language-bash">
echo "--- 3. PUSHING DOCKER IMAGE ---"

# Define image tag using Jenkins build number
export IMAGE_TAG="praveen581348/senderservice:${BUILD_NUMBER}"

echo "Logging in to Docker Hub as $DOCKER_USER..."
echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

echo "Pushing image: $IMAGE_TAG"
docker push $IMAGE_TAG
echo "Push complete."
</code></pre>

<hr>

<h3>✅ Result:</h3>
<p>Jenkins securely logs in using the credential binding, pushes your Docker image with the build number tag, and never exposes the password in logs.</p>

<hr/>
<hr/>
# 📝 Jenkins Lab Notes: Phase 1.3 (Final Step) — The Helm Deploy Step

<hr>

<h2>🎯 Overview</h2>
<p>This final step completes your <b>Sender Service</b> CI/CD pipeline. It runs after the Maven build and Docker image push, deploying the newly built container to your Kubernetes cluster using <b>Helm</b>.</p>

<hr>

<h2>⚙️ 1. The "Execute Shell" Command</h2>
<p>This is the script configured in your Freestyle job's final <b>Build</b> step.</p>

<pre><code class="language-bash">
echo "--- 4. DEPLOYING TO KUBERNETES VIA HELM ---"

# 1. Navigate into the 'helm' directory
cd helm

echo "Upgrading Helm release 'senderservice'..."

# 2. Run the Helm Upgrade command
helm upgrade --install senderservice ./senderservice-chart   -n sender   --set image.tag=${BUILD_NUMBER}   --wait
  
echo "Helm upgrade complete. Rolling update started."
</code></pre>

<hr>

<h2>🔍 2. In-Depth Breakdown of the Helm Command</h2>
<p>Here's what each part of the <code>helm upgrade</code> command does:</p>

<table border="1" cellspacing="0" cellpadding="5">
<tr><th>Command / Flag</th><th>Meaning</th><th>Purpose</th></tr>
<tr><td><code>helm upgrade</code></td><td>Updates an existing release with new chart/configuration.</td><td>Standard command for CI/CD deployments.</td></tr>
<tr><td><code>--install</code></td><td>Installs the chart if the release doesn’t already exist.</td><td>Makes deployment idempotent — works on first and subsequent runs.</td></tr>
<tr><td><code>senderservice</code></td><td>The Helm <b>Release Name</b>.</td><td>Identifies this specific deployment instance.</td></tr>
<tr><td><code>./senderservice-chart</code></td><td>Path to your Helm chart folder.</td><td>Contains <code>Chart.yaml</code> and <code>templates/</code>.</td></tr>
<tr><td><code>-n sender</code></td><td>Kubernetes namespace.</td><td>Deploys resources into <b>sender</b> namespace.</td></tr>
<tr><td><code>--set image.tag=${BUILD_NUMBER}</code></td><td>Overrides image tag in <code>values.yaml</code> dynamically.</td><td>Deploys the image built by the current Jenkins build.</td></tr>
<tr><td><code>--wait</code></td><td>Waits for all Pods to become ready before exiting.</td><td>Ensures stability; fails build if rollout fails.</td></tr>
</table>

<hr>

<h2>🔐 3. Authentication & RBAC (Your Fix Explained)</h2>

<p>When you run <code>helm upgrade</code> from Jenkins, Helm uses your in-cluster Kubernetes Service Account for authentication.</p>

<ol>
<li><b>Identity:</b> Jenkins Pod runs in the <code>jenkins</code> namespace using <code>serviceAccountName: jenkins-sa</code>.</li>
<li><b>Authentication:</b> Helm reads the in-cluster kubeconfig token for <code>jenkins-sa</code>.</li>
<li><b>Authorization:</b> API server verifies permissions using <code>RoleBinding</code> → <code>Role</code> mapping.</li>
<li><b>Permission Granted:</b> You added <code>replicasets</code> to your Role, allowing Helm to manage Deployments and ReplicaSets.</li>
</ol>

<p><b>✅ Result:</b> Helm now has full permission to perform rolling updates in the <code>sender</code> namespace.</p>

<hr>

<h2>🚀 4. What Happens in Kubernetes (The Rolling Update)</h2>

<p>Once authorized, Helm performs a rolling update:</p>

<ol>
<li>Compares current deployment (e.g., tag <code>:6</code>) with new chart (tag <code>:7</code>).</li>
<li>Detects a change in <code>spec.template.spec.containers[0].image</code>.</li>
<li>Patches <b>senderservice-senderservice-deployment</b> with the new image tag.</li>
<li>Kubernetes Deployment Controller initiates rolling update:
  <ul>
    <li>Creates new <b>ReplicaSet</b> for tag <code>:7</code>.</li>
    <li>Launches new Pod with <code>praveen581348/senderservice:7</code>.</li>
    <li>Waits for the new Pod to become <b>Ready</b>.</li>
    <li>Terminates the old Pod (tag <code>:6</code>).</li>
  </ul>
</li>
<li><code>helm --wait</code> monitors rollout until success and returns exit code <code>0</code>.</li>
<li>Jenkins marks build as <b>SUCCESS</b>.</li>
</ol>

<hr>

<h2>🏁 Conclusion</h2>
<p>By fixing your RBAC permissions and integrating Helm in your Jenkins pipeline, you have successfully built a complete, production-grade CI/CD system that performs:</p>

<ul>
<li>Automated build and test via Maven</li>
<li>Secure image push to DockerHub</li>
<li>Dynamic Helm-based deployment to Kubernetes</li>
<li>Fully automated rolling updates with rollback safety</li>
</ul>

<p><b>🎉 Congratulations!</b> You now have a fully functional end-to-end CI/CD pipeline for your Sender Service.</p>
<hr/>
<hr/>

<!-- HTML Styled Documentation for GitHub -->

<h1 align="center">🚀 CI Pipeline Setup for Receiver Service Using Jenkins Pipeline Job</h1>

<p align="center">
  <strong>(Detailed Notes + Concepts + Step-by-Step Explanation)</strong>
</p>

---

Creating a CI Pipeline for the **Receiver Service** using Jenkins Pipeline provides a fully automated process for:

✔ Building the Java application with Maven  
✔ Uploading the artifact to Nexus  
✔ Building a Docker image  
✔ Pushing the image to DockerHub  

This documentation explains how the pipeline was configured, what options were selected, and why pipeline jobs are preferred.

---

<h2>🚀 1. Why Use a Jenkins Pipeline Instead of a Freestyle Job?</h2>

<div style="display:flex; gap:20px;">

<div style="flex:1;">
<h3>❌ Freestyle Job (Old style)</h3>
<ul>
  <li>UI-driven</li>
  <li>Hard to maintain</li>
  <li>No version control</li>
  <li>Changing Jenkins UI changes the job</li>
  <li>Not reusable across environments</li>
</ul>
</div>

<div style="flex:1;">
<h3>✔ Pipeline Job (Modern CI/CD)</h3>
<ul>
  <li>Written as code (Jenkinsfile)</li>
  <li>Stored in GitHub — version controlled</li>
  <li>Supports complex logic</li>
  <li>Supports parallel stages</li>
  <li>Triggers automatically via GitHub webhook</li>
  <li>Reusable across Dev, QA, Prod</li>
</ul>
</div>
</div>

<p><strong>📌 In short: Pipeline = CI/CD as Code (Industry Standard)</strong></p>

---

<h2>🏗 2. Creating a Jenkins Pipeline Job – Step by Step</h2>

✔ Go to: <strong>Jenkins Dashboard → New Item</strong>  
✔ Choose: <code>Pipeline</code>  
✔ Enter job name: <code>receiver-pipeline</code>  
✔ Click <strong>OK</strong>

---

<h2>🧰 3. Pipeline Job Configuration Options</h2>

These are important sections inside the Pipeline job configuration screen.

---

<h3>🔹 General Section</h3>

| Option | Why we use it |
|--------|---------------|
| ✔ Discard Old Builds | Prevents Jenkins storage from filling |
| ✔ GitHub Project (optional) | Links build job to GitHub repo |

If enabled, use:

```
https://github.com/praveen581348/receiverservice
```

---

<h3>🔹 Build Triggers Section</h3>

⭐ **Poll SCM:**  
```
H/5 * * * *
```
(Jenkins checks repo every 5 minutes)

⭐ **GitHub hook trigger for GITScm polling:**  
Automatically triggers CI on Git push.

---

<h3>🔹 Advanced Project Options</h3>

Pipelines can later be extended into **Multibranch Pipeline Jobs**.

---

<h2>🔐 4. Credentials Needed & Why</h2>

Your receiver pipeline uses three key credentials:

---

### ✔ 1. Nexus Credentials (`nexus_cred`)

Used for Maven deploy:

```
mvn deploy
```

---

### ✔ 2. DockerHub Credentials (`dockerhub-creds`)

Used for:
```
docker login  
docker push  
```

---

### ✔ 3. Maven settings.xml via Config File Provider (`receiverservice-settings`)

Stores:
- Nexus server ID  
- Nexus username  
- Nexus password  

Loaded using:

```groovy
configFileProvider([configFile(fileId: 'receiverservice-settings', variable: 'MAVEN_SETTINGS')])
```

---

<h2>🧩 5. Pipeline Script Section – Using SCM</h2>

In Jenkins Pipeline config, choose:

```
Definition: Pipeline script from SCM
```

SCM Settings:

| Field | Value |
|-------|--------|
| SCM | Git |
| Repository URL | https://github.com/praveen581348/receiverservice.git |
| Branch | master |
| Script Path | Jenkinsfile |

Jenkins now automatically:

1. Clones your repo  
2. Reads Jenkinsfile  
3. Runs CI pipeline  

This is called **Pipeline-as-Code**.

---

<h2>🧪 6. Explanation of Each Stage in Your Jenkinsfile</h2>

Below is your complete pipeline with explanation.

---

<h3>🟦 Stage 1 — Checkout Code</h3>

```groovy
git url: 'https://github.com/praveen581348/receiverservice.git', branch: 'master'
```
Pulls the latest source code.

---

<h3>🟧 Stage 2 — Build & Push to Nexus</h3>

```groovy
configFileProvider([configFile(fileId: 'receiverservice-settings', variable: 'MAVEN_SETTINGS')]) {
    withCredentials([usernamePassword(credentialsId: 'nexus_cred', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
        sh "mvn clean deploy -DskipTests -s $MAVEN_SETTINGS"
    }
}
```

✔ Loads settings.xml  
✔ Injects Nexus credentials  
✔ Builds JAR  
✔ Uploads artifact to Nexus  

---

<h3>🟩 Stage 3 — Build & Push Docker Image</h3>

Docker build:

```groovy
sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
```

Docker login and push:

```groovy
withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
}
```

Result example:

```
praveen581348/receiverservice:28
```

---

<h2>🟪 7. Post Actions</h2>

```groovy
post {
    success { echo 'Pipeline Succeeded!' }
    failure { echo 'Pipeline Failed! Check logs.' }
}
```

Uses:
- Slack/email notifications  
- Cleanup  
- Build reporting  

---

<h2>⭐ 8. Benefits of Jenkins Pipeline CI for Receiver Service</h2>

✔ CI/CD as Code  
✔ Secure credential handling  
✔ Version controlled pipeline  
✔ Reusable across environments  
✔ Clean logs & visual stages  
✔ Easily extendable (Helm, ArgoCD, SonarQube)

---

<h2>🎯 Final Summary</h2>

This CI pipeline fully automates:

1. Code checkout  
2. Maven build  
3. Deploy to Nexus  
4. Docker build  
5. Docker push  

All actions are safely secured and executed using a Jenkinsfile stored in GitHub.

---

<h3 align="center">💡 This is a production-ready CI pipeline setup using Jenkins Pipeline-as-Code.</h3>
