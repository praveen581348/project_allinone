
<div align="center">

# 🧭 <span style="color:#3b82f6;">Helm Basics </span>

</div>

---

## 🧩 <span style="color:#16a34a;">What is Helm?</span>

Helm is the <b>package manager for Kubernetes</b>, similar to how <code>apt</code> or <code>yum</code> work for Linux.  
It helps you <b>define, install, and manage</b> Kubernetes applications using <b>Helm Charts</b> — reusable templates containing manifests.

> 💡 Think of a Helm Chart as a blueprint for your application’s deployments, services, and resources.

```bash
myapp/
├── Chart.yaml
├── values.yaml
└── templates/
```

---

## 💡 <span style="color:#f97316;">Why Helm is Needed</span>

Without Helm, you must manually manage multiple YAMLs for each component. This quickly becomes error-prone.

### ✅ Helm Advantages
- 📦 Package multiple YAMLs into one versioned chart  
- ⚙️ Parameterize configuration with `values.yaml`  
- 🔁 Easy upgrades and rollbacks  
- 🕒 Track release history  
- 🌍 Share charts across teams via repositories

<table>
<tr><th>Feature</th><th>kubectl + YAML</th><th>Helm</th></tr>
<tr><td>Deployment</td><td>Multiple files</td><td><code>helm install</code></td></tr>
<tr><td>Config</td><td>Hardcoded</td><td>Dynamic via <code>values.yaml</code></td></tr>
<tr><td>Updates</td><td>Manual edits</td><td><code>helm upgrade</code></td></tr>
<tr><td>Rollback</td><td>Manual revert</td><td><code>helm rollback</code></td></tr>
<tr><td>Sharing</td><td>Hard to share</td><td>Chart repositories</td></tr>
</table>

---

## 🏗️ <span style="color:#0ea5e9;">Helm Architecture Overview</span>

### 🧱 1. Chart
A Helm package containing templates and metadata.

### 🚀 2. Release
A running instance of a chart in a cluster.

```bash
helm install frontend ./myapp
helm install backend ./myapp
```

### 🗂️ 3. Repository
A collection of charts that can be shared.
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm search repo nginx
```

---

## ⚖️ <span style="color:#a855f7;">Helm v2 vs Helm v3</span>

| Feature | Helm v2 | Helm v3 |
|----------|----------|----------|
| **Tiller** | Required (server-side) | Removed |
| **Security** | Needs RBAC setup | Uses kubeconfig directly |
| **Installation** | Client + Tiller | Client-only |
| **Storage** | ConfigMaps | Kubernetes Secrets |

✅ **Helm 3** is simpler, secure, and recommended for all environments.

---

## ⚙️ <span style="color:#f59e0b;">Helm 3 — Key Improvements</span>

1. 🚫 Removed Tiller — fully client-side  
2. 🔐 Better security (no cluster-wide roles)  
3. 📦 Release data stored as Kubernetes Secrets  
4. 🧩 Supports library charts for reuse  
5. 🧠 Improved CRD handling  
6. 🪝 Enhanced hooks for lifecycle management

---

## 🧰 <span style="color:#10b981;">Installing Helm in WSL</span>

### Prerequisites
Ensure you have:
```bash
kubectl version --client
kubectl get nodes
```

### 🌀 Option 1 — Install via Script
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

### 📦 Option 2 — Manual Installation
```bash
wget https://get.helm.sh/helm-v3.15.1-linux-amd64.tar.gz
tar -zxvf helm-v3.15.1-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
helm version
```

---

## 🧪 <span style="color:#3b82f6;">Verify Setup</span>

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo nginx
helm install mynginx bitnami/nginx
kubectl get pods
kubectl get svc
```

If you see NGINX pods running, Helm is working! 🎉

---

## 🧠 <span style="color:#8b5cf6;">How Helm Works in WSL</span>

- Helm uses your kubeconfig from inside WSL: `~/.kube/config`
- If your cluster is running on Windows (e.g., Docker Desktop), copy config to WSL:
```bash
cp /mnt/c/Users/<your-username>/.kube/config ~/.kube/config
```

---

## ✅ <span style="color:#0ea5e9;">Quick Summary</span>

| Step | Command | Description |
|------|----------|-------------|
| 1 | `sudo apt install kubectl` | Install kubectl |
| 2 | `curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash` | Install Helm |
| 3 | `helm version` | Verify installation |
| 4 | `helm repo add bitnami https://charts.bitnami.com/bitnami` | Add Bitnami repo |
| 5 | `helm install mynginx bitnami/nginx` | Deploy sample app |
| 6 | `kubectl get pods` | Confirm deployment |

---

<div align="center">

## 🎯 <span style="color:#22c55e;">Summary</span>

✨ **Helm** simplifies Kubernetes application management.  
📦 **Charts** = reusable blueprints.  
🚀 **Releases** = tracked deployments.  
🔁 **Upgrades & Rollbacks** = easy with one command.  
💻 Works seamlessly in **WSL** and **Helm 3** is secure and modern.

</div>
<hr/>
<hr/>

<div align="center">

# 🧩 <span style="color:#3b82f6;">Learn Helm Chart Structure</span>

</div>

---

## 🎯 <span style="color:#22c55e;">Goal</span>

Understand how a **Helm Chart** is organized — what each file does, how templates and values work together, and how to deploy your own chart.

---

## 🧱 <span style="color:#f59e0b;">What is a Helm Chart?</span>

A **Helm Chart** is a package of Kubernetes manifests that defines a deployable unit.  
Each chart contains templates, default configurations, and metadata required to deploy an app on Kubernetes.

It’s like a **blueprint** of your application deployment.

---

## 🗂️ <span style="color:#0ea5e9;">Helm Chart Directory Structure</span>

When you run:

```bash
helm create myapp
```

Helm scaffolds a chart folder like this:

```bash
myapp/
├── .helmignore
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   └── NOTES.txt
```

---

## 📜 <span style="color:#f97316;">1. Chart.yaml</span>

The **metadata file** for your chart.  
Defines chart name, version, description, and dependencies.

```yaml
apiVersion: v2
name: myapp
description: A simple NGINX deployment
type: application
version: 0.1.0
appVersion: "1.21.1"
```

| Field | Description |
|--------|--------------|
| `apiVersion` | Must be `v2` for Helm 3 |
| `name` | Chart name |
| `description` | Short info about chart |
| `type` | `application` (deployable app) or `library` |
| `version` | Chart version |
| `appVersion` | Actual app version |

---

## ⚙️ <span style="color:#16a34a;">2. values.yaml</span>

This file contains **default configuration values** for your chart.

Override values using:
```bash
helm install myapp ./myapp -f custom-values.yaml
# or
helm install myapp ./myapp --set image.tag=2.0
```

Example:
```yaml
replicaCount: 2
image:
  repository: nginx
  tag: latest
  pullPolicy: IfNotPresent
service:
  type: ClusterIP
  port: 80
```

Referenced in templates:
```yaml
image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
```

---

## 🧩 <span style="color:#a855f7;">3. templates/ Folder</span>

Contains Kubernetes manifest templates rendered using Go templating.

| File | Description |
|------|--------------|
| `deployment.yaml` | App deployment |
| `service.yaml` | Service definition |
| `ingress.yaml` | Ingress rules |
| `_helpers.tpl` | Helper template functions |
| `NOTES.txt` | Post-installation info |
| `hpa.yaml` | Optional autoscaling config |

Example: `deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "myapp.name" . }}
  template:
    metadata:
      labels:
        app: {{ include "myapp.name" . }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: 80
```

---

## 🧰 <span style="color:#0ea5e9;">4. _helpers.tpl</span>

A **powerful file** that defines reusable template snippets (like functions).  
Used to maintain DRY (Don’t Repeat Yourself) principles.

### Example:
```gotemplate
{{- define "myapp.name" -}}
{{ .Chart.Name }}
{{- end -}}

{{- define "myapp.fullname" -}}
{{ printf "%s-%s" .Release.Name .Chart.Name | trunc 63 | trimSuffix "-" }}
{{- end -}}

{{- define "myapp.labels" -}}
app.kubernetes.io/name: {{ include "myapp.name" . }}
app.kubernetes.io/instance: {{ .Release.Name }}
app.kubernetes.io/version: {{ .Chart.AppVersion }}
{{- end -}}
```

Usage inside templates:
```yaml
metadata:
  name: {{ include "myapp.fullname" . }}
  labels:
    {{- include "myapp.labels" . | nindent 4 }}
```

🧩 Benefits:
- Centralized naming
- Consistent labels
- Cleaner YAML templates

---

## 🧾 <span style="color:#f59e0b;">5. NOTES.txt</span>

Displays helpful info after chart installation.

Example:
```text
Thank you for installing {{ .Chart.Name }}!

To access your application:
  export POD_NAME=$(kubectl get pods --namespace {{ .Release.Namespace }} -l "app.kubernetes.io/name={{ .Chart.Name }}" -o jsonpath="{.items[0].metadata.name}")
  kubectl port-forward $POD_NAME 8080:80
  Visit: http://127.0.0.1:8080
```

---

## 📦 <span style="color:#8b5cf6;">6. charts/ Folder</span>

Contains **dependencies** (subcharts).  
When a chart uses another chart (e.g., Redis), dependencies are placed here via:

```bash
helm dependency update
```

---

## 🧪 <span style="color:#22c55e;">Practice: Deploying a Simple NGINX Chart</span>

### Step 1 — Create a chart
```bash
helm create nginx-demo
```

### Step 2 — Explore structure
```bash
tree nginx-demo
```

### Step 3 — Modify values.yaml
```yaml
image:
  repository: nginx
  tag: "latest"
```

### Step 4 — Deploy
```bash
helm install nginx-demo ./nginx-demo
```

### Step 5 — Verify
```bash
kubectl get pods
kubectl get svc
```

### Step 6 — Test Access
```bash
kubectl port-forward svc/nginx-demo 8080:80
```
Then visit → **http://localhost:8080**

### Cleanup:
```bash
helm uninstall nginx-demo
```

---

## 🧠 <span style="color:#0ea5e9;">Quick Recap</span>

| Component | Purpose |
|------------|----------|
| `Chart.yaml` | Metadata for the chart |
| `values.yaml` | Default configs |
| `templates/` | YAML templates |
| `_helpers.tpl` | Helper functions |
| `NOTES.txt` | Post-install info |
| `charts/` | Dependencies |
| `.helmignore` | Ignore files on packaging |

---

## 💡 <span style="color:#f97316;">Debug Tip</span>

Preview chart rendering without installing:
```bash
helm template myapp ./myapp
```

---

<div align="center">

## 🎯 <span style="color:#22c55e;">Summary</span>

✨ A **Helm Chart** organizes your Kubernetes manifests with templates, values, and metadata.  
🧩 The `_helpers.tpl` ensures DRY and consistency.  
📦 Everything from config → templates → release is modular.  
🚀 Once you master chart structure, creating reusable Kubernetes apps becomes easy.

</div>
<hr/>
<hr/>

<div align="center">
  <h1>🧭 Step 3: Helm Commands in Depth</h1>
  <p><b>Goal:</b> Learn how to manage Helm releases & deployments with confidence.</p>
</div>

---

<h2>🎯 Overview</h2>
<p>
Helm is the package manager for Kubernetes. Once you understand charts and templates, the next key step is learning <b>how to manage releases</b> — the actual running instances of those charts.<br><br>
Every Helm command deals with a <b>release</b> — which represents one deployment of your chart into a cluster.
</p>

---

<h2>🧩 What is a Helm Release Name?</h2>
<p>
A <b>Helm release</b> is a <b>running instance</b> of a chart in your Kubernetes cluster. When you install a chart, you assign it a unique name — this becomes your <b>release name</b>.
</p>

<p>Think of it like this:</p>
<ul>
  <li>📦 <b>Chart</b> → Blueprint of an application (e.g., nginx)</li>
  <li>🚀 <b>Release</b> → A running copy of that chart (e.g., my-nginx)</li>
</ul>

<h3>Example</h3>
<pre><code>helm install my-nginx bitnami/nginx</code></pre>
<p>Here:<br>
• Chart → bitnami/nginx<br>
• Release name → <b>my-nginx</b>
</p>

<p>You can check it using:</p>
<pre><code>helm list</code></pre>

Output:
<pre><code>NAME        NAMESPACE  STATUS    CHART
my-nginx    default    deployed  nginx-13.2.21
</code></pre>

---

<h2>🌐 Release Names Across Environments</h2>
<p>
You can use <b>different release names</b> to deploy the same chart into multiple environments (dev, qa, prod).
</p>

<table>
<tr><th>Environment</th><th>Release Name</th><th>Namespace</th><th>Values File</th></tr>
<tr><td>Dev</td><td>myapp-dev</td><td>dev</td><td>values-dev.yaml</td></tr>
<tr><td>QA</td><td>myapp-qa</td><td>qa</td><td>values-qa.yaml</td></tr>
<tr><td>Prod</td><td>myapp-prod</td><td>prod</td><td>values-prod.yaml</td></tr>
</table>

<h3>Example Commands</h3>
<pre><code>helm install myapp-dev ./myapp -f values-dev.yaml -n dev
helm install myapp-qa ./myapp -f values-qa.yaml -n qa
helm install myapp-prod ./myapp -f values-prod.yaml -n prod
</code></pre>

Each environment has its own release, configs, and history.

---

<h2>⚙️ Helm Core Commands</h2>
<p>These are the main commands used to manage the Helm lifecycle.</p>

<h3>1️⃣ helm install — Install a Chart</h3>
<p>Deploys a new Helm release (application) into your cluster.</p>
<pre><code>helm install &lt;release-name&gt; &lt;chart&gt; [flags]</code></pre>

Example:
<pre><code>helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-nginx bitnami/nginx
</code></pre>

Tips:
<pre><code>helm install my-nginx bitnami/nginx --set service.type=NodePort
helm install my-nginx bitnami/nginx -f custom-values.yaml
</code></pre>

---

<h3>2️⃣ helm upgrade — Upgrade a Release</h3>
<p>Used to update an existing release with new values, chart versions, or configurations.</p>
<pre><code>helm upgrade &lt;release-name&gt; &lt;chart&gt; [flags]</code></pre>

Example:
<pre><code>helm upgrade my-nginx bitnami/nginx --set replicaCount=3
</code></pre>

With rollback safety:
<pre><code>helm upgrade my-nginx bitnami/nginx --set image.tag=latest --atomic
</code></pre>

---

<h3>3️⃣ helm rollback — Roll Back to a Previous Version</h3>
<p>Revert your release to an older revision.</p>
<pre><code>helm rollback &lt;release-name&gt; &lt;revision&gt;</code></pre>

View history first:
<pre><code>helm history my-nginx</code></pre>

Then rollback:
<pre><code>helm rollback my-nginx 1</code></pre>

This restores all Kubernetes resources to the state they were in during revision 1.

---

<h3>4️⃣ helm uninstall — Delete a Release</h3>
<p>Removes the deployed application and its resources.</p>
<pre><code>helm uninstall &lt;release-name&gt;</code></pre>

Example:
<pre><code>helm uninstall my-nginx</code></pre>

Tip: Keep history (for auditing or rollback info)
<pre><code>helm uninstall my-nginx --keep-history
</code></pre>

---

<h3>5️⃣ helm list — View Active Releases</h3>
<p>Lists all Helm releases in a cluster or specific namespace.</p>
<pre><code>helm list [flags]</code></pre>

Example:
<pre><code>helm list -A
</code></pre>

Output:
<pre><code>NAME        NAMESPACE  STATUS    CHART
my-nginx    default    deployed  nginx-13.2.21
</code></pre>

---

<h3>6️⃣ helm history — View Release Revision History</h3>
<p>Shows all revisions (installations, upgrades, rollbacks) of a release.</p>
<pre><code>helm history &lt;release-name&gt;</code></pre>

Example:
<pre><code>helm history my-nginx
</code></pre>

Output:
<pre><code>REVISION  UPDATED               STATUS     CHART        APP VERSION  DESCRIPTION
1         2025-10-27 15:00:00  deployed   nginx-13.2.21 1.21.6      Install complete
2         2025-10-28 11:45:00  deployed   nginx-13.2.22 1.21.7      Upgrade complete
</code></pre>

---

<h2>🧠 Hands-On Practice</h2>
<p>Try this practical sequence to master Helm commands:</p>

<pre><code># 1️⃣ Install
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install demo-nginx bitnami/nginx

# 2️⃣ Check
helm list

# 3️⃣ Upgrade
helm upgrade demo-nginx bitnami/nginx --set replicaCount=3

# 4️⃣ View history
helm history demo-nginx

# 5️⃣ Rollback
helm rollback demo-nginx 1

# 6️⃣ Uninstall
helm uninstall demo-nginx
</code></pre>

---

<h2>📘 Command Summary Table</h2>

<table>
<tr><th>Command</th><th>Description</th><th>Example</th></tr>
<tr><td><code>helm install</code></td><td>Install a chart as a release</td><td><code>helm install myapp ./mychart</code></td></tr>
<tr><td><code>helm upgrade</code></td><td>Upgrade an existing release</td><td><code>helm upgrade myapp ./mychart -f values.yaml</code></td></tr>
<tr><td><code>helm rollback</code></td><td>Rollback to previous revision</td><td><code>helm rollback myapp 1</code></td></tr>
<tr><td><code>helm uninstall</code></td><td>Delete a release</td><td><code>helm uninstall myapp</code></td></tr>
<tr><td><code>helm list</code></td><td>List all releases</td><td><code>helm list -A</code></td></tr>
<tr><td><code>helm history</code></td><td>View release history</td><td><code>helm history myapp</code></td></tr>
</table>

---

<h2>🏁 Key Takeaways</h2>
<ul>
  <li>Every Helm <b>release</b> is a versioned deployment tracked in Kubernetes.</li>
  <li>You can <b>upgrade</b>, <b>rollback</b>, and <b>remove</b> releases easily.</li>
  <li>Use <b>different release names</b> for different environments (dev, qa, prod).</li>
  <li>Helm simplifies app lifecycle management — no need to handle multiple YAMLs manually.</li>
</ul>

---

<div align="center">
  <b>✅ Helm makes Kubernetes deployments repeatable, versioned, and safe.</b>
</div>
<hr/>
<hr/>

<h1>🧭 Step 4 — Working with Values & Templates (Deep Dive)</h1>

<p><strong>Goal:</strong> Learn how to use <code>values.yaml</code> and Helm templating so charts are dynamic, DRY, and safe to override for different environments.</p>

<hr>

<h2>1️⃣ values.yaml — the configuration surface</h2>

<p><code>values.yaml</code> holds defaults for your chart. Think of it as variables your templates will read.</p>

<pre><code class="language-yaml">replicaCount: 2

image:
  repository: myorg/myapp
  tag: "1.0.0"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80

resources: {}</code></pre>

<ul>
  <li>Keep values structured, not flat.</li>
  <li>Set sensible defaults.</li>
  <li>Use clear names (e.g., <code>image.repository</code>).</li>
</ul>

<hr>

<h2>2️⃣ Referencing values in templates</h2>

<pre><code class="language-gotemplate">{{ .Values.replicaCount }}
{{ .Values.image.repository }}</code></pre>

<pre><code class="language-yaml">apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
</code></pre>

<hr>

<h2>3️⃣ Overriding values</h2>

<p>You can override chart values at install/upgrade time:</p>

<pre><code>helm install myapp ./mychart -f values-prod.yaml
helm install myapp ./mychart --set image.tag=2.0.0 --set replicaCount=3</code></pre>

<p>For nested keys:</p>
<pre><code>--set image.tag=2.0.0</code></pre>

<p>Use <code>--set-file</code> for files, <code>--values</code> for merging multiple files, <code>--set-string</code> to force string type.</p>

<hr>

<h2>4️⃣ Go templating basics</h2>

<p>Helm uses Go templates with additional functions (Sprig + Helm functions).</p>

<ul>
<li><code>default</code> → provide a fallback value</li>
<li><code>toYaml</code> + <code>nindent</code> → render nested blocks</li>
<li><code>quote</code>, <code>printf</code>, <code>required</code></li>
</ul>

<pre><code class="language-gotemplate">replicas: {{ .Values.replicaCount | default 1 }}
image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"</code></pre>

<hr>

<h2>5️⃣ Conditional logic (if, else, with)</h2>

<pre><code class="language-gotemplate">{{- if .Values.service.annotations }}
metadata:
  annotations:
    {{ toYaml .Values.service.annotations | nindent 4 }}
{{- end }}</code></pre>

<pre><code class="language-gotemplate">{{- with .Values.podAnnotations }}
metadata:
  annotations:
  {{ toYaml . | nindent 4 }}
{{- end }}</code></pre>

<hr>

<h2>6️⃣ Looping (range)</h2>

<pre><code class="language-gotemplate">{{- range .Values.env }}
- name: {{ .name }}
  value: {{ .value | quote }}
{{- end }}</code></pre>

<pre><code class="language-gotemplate">{{- range $key, $val := .Values.labels }}
{{ $key }}: {{ $val | quote }}
{{- end }}</code></pre>

<hr>

<h2>7️⃣ _helpers.tpl + include</h2>

<p>Define reusable snippets in <code>_helpers.tpl</code> and include them elsewhere.</p>

<pre><code class="language-gotemplate">{{- define "myapp.name" -}}
{{ .Chart.Name }}
{{- end -}}
</code></pre>

<p>Use it in other templates:</p>
<pre><code class="language-gotemplate">metadata:
  name: {{ include "myapp.fullname" . }}
</code></pre>

<hr>

<h2>8️⃣ Rendering & Debugging Templates</h2>

<pre><code>helm template myrelease ./mychart -f values.yaml
helm install myrelease ./mychart --dry-run --debug -f values.yaml</code></pre>

<hr>

<h2>9️⃣ Complete Example</h2>

<pre><code class="language-bash">mychart/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── _helpers.tpl
    ├── deployment.yaml
    ├── service.yaml
    └── NOTES.txt</code></pre>

<p>Example <code>values.yaml</code>:</p>

<pre><code class="language-yaml">replicaCount: 2
image:
  repository: nginx
  tag: "1.23.1"
service:
  type: ClusterIP
  port: 80</code></pre>

<p>Deployment uses dynamic values from above.</p>

<hr>

<h2>🔧 Practice Exercise</h2>

<ol>
<li>Create a new chart: <code>helm create mychart</code></li>
<li>Edit <code>values.yaml</code> to include image & replicaCount.</li>
<li>Render locally: <code>helm template mychart</code></li>
<li>Override: <code>helm upgrade mychart --set image.tag=1.25.0</code></li>
<li>Rollback if needed: <code>helm rollback mychart 1</code></li>
</ol>

<hr>

<h2>💡 Recap</h2>

<ul>
<li>Use <code>values.yaml</code> for config.</li>
<li>Override safely with <code>--set</code> / <code>--values</code>.</li>
<li>Keep templates clean & DRY with <code>_helpers.tpl</code>.</li>
<li>Validate using <code>helm lint</code>.</li>
<li>Render with <code>helm template</code> before installing.</li>
</ul>

<hr/>
<hr/>

<h1 align="center">🧭  Helm Repositories</h1>

<h3>🎯 Goal</h3>
<p>Learn how to <strong>discover, add, use, and share</strong> Helm charts through repositories.</p>

<hr/>

<h2>🚀 1️⃣ What is a Helm Repository?</h2>
<p>A <strong>Helm repository</strong> is like an <em>app store</em> for Kubernetes applications.  
It’s a web (HTTP/HTTPS) endpoint that hosts packaged chart archives (<code>.tgz</code>) along with an <strong>index.yaml</strong> file describing all available charts.</p>

<p><b>Analogy:</b></p>
<ul>
<li>🏬 <b>Helm Repo</b> → App Store</li>
<li>📦 <b>Helm Chart</b> → App Package</li>
<li>🚀 <b>Release</b> → Installed App Instance</li>
</ul>

<p>When you run <code>helm repo add</code>, you’re essentially subscribing to a remote chart catalog.</p>

<hr/>

<h2>🧩 2️⃣ Repo Structure</h2>

<pre><code>index.yaml
mychart-1.0.0.tgz
mychart-1.1.0.tgz
</code></pre>

<p><b>index.yaml contents:</b></p>

<pre><code class="language-yaml">apiVersion: v1
entries:
  mychart:
    - version: 1.0.0
      urls:
        - mychart-1.0.0.tgz
</code></pre>

<p>Helm uses this file to know what versions exist and where to download them.</p>

<hr/>

<h2>🌐 3️⃣ Adding Repositories</h2>

<pre><code class="language-bash">helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo list
helm repo update
</code></pre>

<p><b>Example Output:</b></p>
<pre><code>NAME     URL
bitnami  https://charts.bitnami.com/bitnami
</code></pre>

<hr/>

<h2>🔍 4️⃣ Searching Charts</h2>

<pre><code class="language-bash">helm search repo nginx
</code></pre>

<p><b>Example Output:</b></p>
<pre><code>NAME                    CHART VERSION  APP VERSION  DESCRIPTION
bitnami/nginx           15.3.2         1.25.1       NGINX web server
bitnami/nginx-ingress   12.1.0         1.9.4        NGINX Ingress Controller
</code></pre>

<hr/>

<h2>🏪 5️⃣ Popular Repositories</h2>

<table>
<tr><th>Repository</th><th>URL</th><th>Description</th></tr>
<tr><td><b>Bitnami</b></td><td>https://charts.bitnami.com/bitnami</td><td>Trusted, production-ready apps</td></tr>
<tr><td><b>Artifact Hub</b></td><td>https://artifacthub.io</td><td>Central registry of Helm charts</td></tr>
<tr><td><b>Helm Stable (Deprecated)</b></td><td>—</td><td>Old official repo (now moved to Artifact Hub)</td></tr>
</table>

<p>You can explore charts directly at <a href="https://artifacthub.io" target="_blank">Artifact Hub</a>.</p>

<hr/>

<h2>🧰 6️⃣ Installing Charts from Repos</h2>

<pre><code class="language-bash">helm install mydb bitnami/mysql
</code></pre>

<p>➡️ <b>mydb</b> → release name<br/>
➡️ <b>bitnami/mysql</b> → repo/chart<br/>
Helm automatically downloads the chart, renders templates, and deploys it to your cluster.</p>

<p><b>Override Values:</b></p>
<pre><code class="language-bash">helm install mydb bitnami/mysql --set auth.rootPassword=admin123
</code></pre>

<p><b>Upgrade Example:</b></p>
<pre><code class="language-bash">helm upgrade mydb bitnami/mysql --set auth.database=mydb2
</code></pre>

<hr/>

<h2>🧑‍💻 7️⃣ Pushing Your Own Charts</h2>

<h3>🅰️ Host as Static Files (GitHub Pages / S3)</h3>

<pre><code class="language-bash">helm package mychart/
helm repo index . --url https://yourdomain.com/charts
</code></pre>

<p>Upload the generated <code>.tgz</code> and <code>index.yaml</code> files.  
Then others can add your repo:</p>

<pre><code class="language-bash">helm repo add myrepo https://yourdomain.com/charts
</code></pre>

<h3>🅱️ Push to OCI Registry (Helm v3.8+)</h3>

<pre><code class="language-bash">helm registry login registry-1.docker.io
helm chart save ./mychart registry-1.docker.io/username/mychart:1.0.0
helm chart push registry-1.docker.io/username/mychart:1.0.0
</code></pre>

<p>To install later:</p>
<pre><code class="language-bash">helm install myapp oci://registry-1.docker.io/username/mychart --version 1.0.0
</code></pre>

<hr/>

<h2>🧠 8️⃣ Best Practices</h2>
<ul>
<li>Keep chart versions <b>semantic</b> (e.g., 1.2.0 → minor update).</li>
<li>Always run <code>helm lint</code> before publishing.</li>
<li>Tag and sign charts (<code>helm verify</code>).</li>
<li>Use CI/CD pipelines to package and update <code>index.yaml</code>.</li>
</ul>

<hr/>

<h2>🧩 9️⃣ Hands-On Exercise</h2>

<pre><code class="language-bash">helm repo add bitnami https://charts.bitnami.com/bitnami
helm search repo mysql
helm install mydb bitnami/mysql --set auth.rootPassword=mysecret
kubectl get pods
helm upgrade mydb bitnami/mysql --set auth.database=sampledb
helm uninstall mydb
</code></pre>

<hr/>

<h2>🏁 Key Takeaways</h2>

<ul>
<li>✅ Helm repositories store versioned chart packages.</li>
<li>✅ Use <code>helm repo add</code> and <code>helm repo update</code> to manage repos.</li>
<li>✅ Install charts via <code>helm install &lt;release&gt; &lt;repo/chart&gt;</code>.</li>
<li>✅ Host repos on GitHub Pages or OCI registries.</li>
<li>✅ Artifact Hub is the central search engine for charts.</li>
</ul>


<hr/>
<hr/>


<h1 align="center">🧭 Creating & Publishing Your Own Helm Chart</h1>

<p><b>Goal:</b> Learn how to <b>build, version, and distribute</b> your own Helm charts — privately or publicly.</p>

<hr>

<h2>🎯 Concept Overview</h2>
<p>Helm charts are reusable application packages. You’ve learned how to install and manage existing charts — now, let’s build and publish your own!</p>

<hr>

<h2>🧱 1️⃣ Create a New Chart</h2>

<pre><code>helm create myapp</code></pre>

<p>This command scaffolds a new Helm chart with the following structure:</p>

<pre><code>myapp/
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl
│   └── NOTES.txt
└── templates/tests/
</code></pre>

<h3>📂 Key Files</h3>
<table>
<tr><th>File</th><th>Description</th></tr>
<tr><td><code>Chart.yaml</code></td><td>Chart metadata (name, version, description)</td></tr>
<tr><td><code>values.yaml</code></td><td>Default configuration values</td></tr>
<tr><td><code>templates/</code></td><td>Kubernetes manifest templates</td></tr>
<tr><td><code>_helpers.tpl</code></td><td>Reusable template helpers</td></tr>
<tr><td><code>NOTES.txt</code></td><td>Instructions shown after installation</td></tr>
</table>

<hr>

<h2>🧩 2️⃣ Customize the Chart</h2>

<p>Update <code>Chart.yaml</code>:</p>

<pre><code>apiVersion: v2
name: myapp
description: A demo Helm chart for my web app
type: application
version: 1.0.0
appVersion: "1.0"
</code></pre>

<p>Edit <code>values.yaml</code>:</p>

<pre><code>replicaCount: 2

image:
  repository: nginx
  tag: "1.25"
  pullPolicy: IfNotPresent

service:
  type: ClusterIP
  port: 80
</code></pre>

<p>Update <code>deployment.yaml</code> to use dynamic values:</p>

<pre><code>apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "myapp.fullname" . }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ include "myapp.name" . }}
  template:
    metadata:
      labels:
        app: {{ include "myapp.name" . }}
    spec:
      containers:
      - name: {{ include "myapp.name" . }}
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        ports:
        - containerPort: {{ .Values.service.port }}
</code></pre>

<hr>

<h2>⚙️ 3️⃣ Package the Chart</h2>

<pre><code>helm package myapp</code></pre>

<p>This creates <code>myapp-1.0.0.tgz</code> — your distributable chart.</p>

<hr>

<h2>🌐 4️⃣ Host Your Chart Repository</h2>

<p>A Helm repo = static files (<code>.tgz</code> + <code>index.yaml</code>). You can host it on:</p>

<ul>
<li>GitHub Pages</li>
<li>AWS S3 / GCS</li>
<li>Artifactory / Harbor</li>
<li>ChartMuseum</li>
</ul>

<h3>Example: Host via GitHub Pages</h3>

<pre><code>mkdir helm-repo
mv myapp-1.0.0.tgz helm-repo/
cd helm-repo
helm repo index .
</code></pre>

<p>Then commit and enable GitHub Pages (URL like <code>https://username.github.io/helm-repo</code>).</p>

<pre><code>helm repo add myrepo https://username.github.io/helm-repo
helm repo update
helm search repo myrepo
</code></pre>

<hr>

<h2>🧠 5️⃣ Test Installation</h2>

<pre><code>helm install demo myrepo/myapp
helm install demo myrepo/myapp --set replicaCount=4,image.tag="latest"
</code></pre>

<hr>

<h2>🚀 6️⃣ Versioning Best Practices</h2>

<table>
<tr><th>Type</th><th>Field</th><th>Example</th><th>Description</th></tr>
<tr><td>Chart Version</td><td><code>version</code></td><td>1.0.0</td><td>Version of the chart</td></tr>
<tr><td>App Version</td><td><code>appVersion</code></td><td>"2.3.1"</td><td>Underlying application version</td></tr>
<tr><td>SemVer</td><td>major.minor.patch</td><td>1.2.3</td><td>Follow semantic versioning</td></tr>
</table>

<pre><code>helm package myapp
helm repo index .
</code></pre>

<hr>

<h2>🧰 7️⃣ Push to OCI / ChartMuseum</h2>

<pre><code>helm registry login ghcr.io -u <username>
helm push myapp-1.0.0.tgz oci://ghcr.io/<username>/charts
helm install demo oci://ghcr.io/<username>/charts/myapp --version 1.0.0
</code></pre>

<hr>

<h2>🧠 Hands-On Recap</h2>

<pre><code># 1️⃣ Create chart
helm create myapp

# 2️⃣ Customize values & templates
vim values.yaml

# 3️⃣ Package
helm package myapp

# 4️⃣ Host (GitHub Pages / S3)
helm repo index .

# 5️⃣ Add repo
helm repo add myrepo https://username.github.io/helm-repo

# 6️⃣ Install
helm install mydemo myrepo/myapp
</code></pre>

<hr>

<h2>🏁 Key Takeaways</h2>

<ul>
<li>A Helm chart is a versioned, reusable application package.</li>
<li>Charts can be shared using static repositories or OCI registries.</li>
<li>Version charts properly using SemVer.</li>
<li>Self-host or push to GitHub Pages for quick sharing.</li>
<li>Helm makes Kubernetes app delivery consistent, portable, and automated.</li>
</ul>

<hr>

<p align="center"><b>✅ Helm chart publishing = Reusable, versioned, and sharable Kubernetes apps.</b></p>
<hr/>
<hr/>

<h1 align="center">🧭 Helm Hooks & Lifecycle Management</h1>

<hr/>

<h2>🎯 Goal</h2>
<p>Understand how to use <strong>Helm hooks</strong> to run special Kubernetes jobs or actions during key chart lifecycle events — like before install, after upgrade, or before delete.</p>

<hr/>

<h2>🧩 What Are Helm Hooks?</h2>
<p>Helm hooks are <strong>special annotations</strong> you can add to Kubernetes manifest files inside your chart that tell Helm <em>when</em> those resources should be executed relative to the release lifecycle.</p>

<blockquote>
Helm hooks = “Lifecycle triggers” that run custom Kubernetes resources at specific points during install/upgrade/delete.
</blockquote>

<hr/>

<h2>⚙️ Common Use Cases</h2>

<table>
<tr><th>Use Case</th><th>Example Resource</th><th>Purpose</th></tr>
<tr><td><strong>Database migrations</strong></td><td>Job</td><td>Run a script before app deployment</td></tr>
<tr><td><strong>Data seeding</strong></td><td>Job / Pod</td><td>Load default data before startup</td></tr>
<tr><td><strong>Cleanup</strong></td><td>Job</td><td>Run cleanup tasks before uninstall</td></tr>
<tr><td><strong>Validation</strong></td><td>Job</td><td>Validate config before installing</td></tr>
<tr><td><strong>Backup</strong></td><td>Job</td><td>Backup data before upgrade or delete</td></tr>
</table>

<hr/>

<h2>🔄 Helm Lifecycle Hook Events</h2>

<table>
<tr><th>Hook Event</th><th>Description</th></tr>
<tr><td><code>pre-install</code></td><td>Run before any resources are created</td></tr>
<tr><td><code>post-install</code></td><td>Run after all resources are created</td></tr>
<tr><td><code>pre-upgrade</code></td><td>Run before upgrading a release</td></tr>
<tr><td><code>post-upgrade</code></td><td>Run after a release has been upgraded</td></tr>
<tr><td><code>pre-rollback</code></td><td>Run before rollback</td></tr>
<tr><td><code>post-rollback</code></td><td>Run after rollback completes</td></tr>
<tr><td><code>pre-delete</code></td><td>Run before resources are deleted</td></tr>
<tr><td><code>post-delete</code></td><td>Run after all resources are deleted</td></tr>
<tr><td><code>test</code></td><td>Used for Helm test hooks</td></tr>
</table>

<hr/>

<h2>🧱 Hook Annotation Example</h2>

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: myapp-preinstall
  annotations:
    "helm.sh/hook": pre-install
spec:
  template:
    spec:
      containers:
        - name: preinstall-job
          image: busybox
          command: ['sh', '-c', 'echo "Running pre-install tasks..."']
      restartPolicy: OnFailure
```

<p>This tells Helm to <strong>run this Job before installing</strong> the rest of the chart.</p>

<hr/>

<h2>🧠 Hook Weight (Execution Order)</h2>

```yaml
annotations:
  "helm.sh/hook": pre-install
  "helm.sh/hook-weight": "1"
```
<p>Lower weight runs first (e.g., -2 runs before -1, then 0, then 1).</p>

<hr/>

<h2>♻️ Hook Deletion Policies</h2>

<p>By default, Helm keeps the hook resource after it runs. Use <code>helm.sh/hook-delete-policy</code> to clean it up automatically.</p>

| Policy | Description |
|---------|-------------|
| `hook-succeeded` | Delete after success |
| `hook-failed` | Delete after failure |
| `before-hook-creation` | Delete previous hook before creating new one |

Example:

```yaml
annotations:
  "helm.sh/hook": pre-install
  "helm.sh/hook-delete-policy": hook-succeeded
```

<hr/>

<h2>🧪 Helm Test Example</h2>

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: myapp-test-connection
  annotations:
    "helm.sh/hook": test
spec:
  template:
    spec:
      containers:
        - name: test
          image: busybox
          command: ['sh', '-c', 'wget myapp-service:80 -T 5']
      restartPolicy: Never
```

Run tests:

```bash
helm test <release-name>
```

<hr/>

<h2>🧩 Example Lifecycle Structure</h2>

```plaintext
myapp/
└── templates/
    ├── pre-install-job.yaml
    ├── post-install-job.yaml
    ├── pre-upgrade-job.yaml
    ├── pre-delete-job.yaml
    ├── deployment.yaml
```

Example pre-install job:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pre-install-db-setup
  annotations:
    "helm.sh/hook": pre-install
    "helm.sh/hook-delete-policy": hook-succeeded
spec:
  template:
    spec:
      containers:
        - name: init-db
          image: busybox
          command: ["sh", "-c", "echo Setting up database..."]
      restartPolicy: OnFailure
```

<hr/>

<h2>🧠 Best Practices</h2>

<ul>
<li>✅ Use hooks for one-time actions (setup, cleanup, migration)</li>
<li>✅ Always set deletion policies</li>
<li>✅ Keep hooks idempotent</li>
<li>✅ Use hook weights for ordering</li>
<li>✅ Separate test hooks clearly</li>
</ul>

<hr/>

<h2>🧾 Hands-On Recap</h2>

```bash
# 1️⃣ Create chart
helm create myapp

# 2️⃣ Add pre-install job
# 3️⃣ Add test job

# 4️⃣ Install chart
helm install myapp ./myapp

# 5️⃣ Run tests
helm test myapp
```

<hr/>

<h2>🏁 Key Takeaways</h2>

<ul>
<li><strong>Helm hooks</strong> automate lifecycle actions.</li>
<li>Use for setup, cleanup, validation, or migration tasks.</li>
<li>Manage via annotations, weights, and delete policies.</li>
<li>Combine hooks with <code>helm test</code> for end-to-end validation.</li>
</ul>
