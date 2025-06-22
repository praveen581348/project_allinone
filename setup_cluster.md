<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 style="color:#2F80ED;">⚙️ Setting Up a Local Kubernetes Cluster with kind</h1>

<p>This guide will help you set up a full-featured local Kubernetes cluster using <strong>kind (Kubernetes IN Docker)</strong>. It's designed to help you test and run microservices such as Spring Boot apps, Kafka, Redis, MySQL, MongoDB, Jenkins, Nexus, SonarQube, and more — all locally on your machine.</p>

<hr/>

<h2 style="color:#27AE60;">🔧 What is kind?</h2>
<p><strong>kind</strong> is a tool that lets you run Kubernetes clusters inside Docker containers. It's perfect for:</p>
<ul>
  <li>🧪 Local testing and development</li>
  <li>🔄 CI/CD pipeline testing</li>
  <li>🎓 Learning Kubernetes</li>
</ul>

<hr/>

<h2 style="color:#F39C12;">💻 Setup on macOS</h2>
<ol>
  <li><strong>Install Docker Desktop</strong><br/>
  Download and install from <a href="https://www.docker.com/products/docker-desktop" target="_blank">Docker Desktop</a>. Make sure Docker is running.</li>
  <li><strong>Install kind</strong><br/>
  <code>brew install kind</code></li>
  <li><strong>Install kubectl</strong><br/>
  <code>brew install kubectl</code></li>
</ol>

<hr/>

<h2 style="color:#8E44AD;">🚀 Create Your Cluster</h2>
<p>Create your Kubernetes cluster using the following config file named <code>cluster.yaml</code>:</p>

<pre><code>kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: demo
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30080  # Jenkins
        hostPort: 30080
        protocol: TCP
      - containerPort: 30081  # Nexus
        hostPort: 30081
        protocol: TCP
      - containerPort: 30090  # SonarQube
        hostPort: 30090
        protocol: TCP
      - containerPort: 30094  # Application UIs
        hostPort: 30094
        protocol: TCP
      - containerPort: 30100  # Jenkins agent port
        hostPort: 30100
        protocol: TCP
    extraMounts:
      - hostPath: /var/run/docker.sock
        containerPath: /var/run/docker.sock

  - role: worker
    extraMounts:
      - hostPath: /var/run/docker.sock
        containerPath: /var/run/docker.sock

  - role: worker
    extraMounts:
      - hostPath: /var/run/docker.sock
        containerPath: /var/run/docker.sock</code></pre>

<p>Then run:</p>
<pre><code>kind create cluster --config cluster.yaml</code></pre>

<hr/>

<h2 style="color:#2C3E50;">✅ Verify Cluster Setup</h2>
<p>To make sure everything is up and running, run the following commands:</p>

<ul>
  <li><code>kubectl get nodes</code> – should list your 1 control-plane and 2 worker nodes</li>
  <li><code>kubectl cluster-info</code> – provides master and DNS details</li>
  <li><code>kubectl get pods -A</code> – see if system pods are running correctly</li>
</ul>

<p>You should see all your nodes in <code>Ready</code> state and pods in <code>Running</code> or <code>Completed</code>.</p>

<hr/>

<h2 style="color:#3498DB;">📁 Repository Structure</h2>
<p><strong>GitHub Repo:</strong> <a href="https://github.com/praveen581348/cluster" target="_blank">cluster</a></p>
<ul>
  <li><code>cluster.yaml</code> – contains complete cluster configuration used to set up the environment</li>
</ul>

<hr/>

<h2 style="color:#16A085;">🛠️ Common kind Commands</h2>
<table style="width:100%; border-collapse: collapse;">
  <thead>
    <tr style="background:#f6f8fa;">
      <th style="border:1px solid #ddd; padding:8px;">Task</th>
      <th style="border:1px solid #ddd; padding:8px;">Command</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border:1px solid #ddd; padding:8px;">✅ Create a cluster</td>
      <td style="border:1px solid #ddd; padding:8px;">kind create cluster --config cluster.yaml</td>
    </tr>
    <tr>
      <td style="border:1px solid #ddd; padding:8px;">❌ Delete a cluster</td>
      <td style="border:1px solid #ddd; padding:8px;">kind delete cluster --name demo</td>
    </tr>
    <tr>
      <td style="border:1px solid #ddd; padding:8px;">📋 List clusters</td>
      <td style="border:1px solid #ddd; padding:8px;">kind get clusters</td>
    </tr>
    <tr>
      <td style="border:1px solid #ddd; padding:8px;">📦 Load Docker image</td>
      <td style="border:1px solid #ddd; padding:8px;">kind load docker-image my-image:tag --name demo</td>
    </tr>
  </tbody>
</table>

<hr/>

<h2 style="color:#9B59B6;">🔗 Learn More</h2>
<ul>
  <li><a href="https://chatgpt.com/share/6857e7f1-2d24-8001-88c5-41d0bf8c0c51" target="_blank">📘 kind  Documentation</a></li>
  <li><a href="https://chatgpt.com/share/6857e648-5de0-8001-ab14-7897f0aa5989" target="_blank">☸️ Kubernetes Docs</a></li>
</ul>

</div>
