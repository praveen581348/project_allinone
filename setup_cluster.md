<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1>🚀 Setting Up Your Local Kubernetes Cluster with kind</h1>

<p>This guide helps you set up a local Kubernetes cluster using <strong>kind</strong> (Kubernetes IN Docker). It's optimized for local development and CI environments and forms the foundation for Phase 1 of our project.</p>

<hr/>

<h2>🔧 What is kind?</h2>
<p><strong>kind</strong> stands for Kubernetes IN Docker. It lets us run Kubernetes clusters in Docker containers, ideal for:</p>
<ul>
  <li>🧪 Local testing and development</li>
  <li>📦 CI/CD pipelines and sandboxing</li>
  <li>🎓 Learning Kubernetes without a cloud setup</li>
</ul>
<img src="./kind.png" alt="kind cluster architecture" width="800"/> <hr/>
<hr/>

<h2>💻 Prerequisites for macOS</h2>
<ol>
  <li><strong>Install Docker Desktop:</strong> <a href="https://www.docker.com/products/docker-desktop" target="_blank">Download Docker</a></li>
  <li><strong>Install kind:</strong> <code>brew install kind</code></li>
  <li><strong>Install kubectl:</strong> <code>brew install kubectl</code></li>
</ol>

<hr/>

<h2>🛠️ cluster.yaml File</h2>
<p>This configuration defines a cluster with 1 control-plane node and 2 worker nodes, and maps multiple container ports to host ports.</p>

<pre><code>kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: demo
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30094
        hostPort: 30094
        protocol: TCP
      - containerPort: 30091
        hostPort: 30091
        protocol: TCP
      - containerPort: 30092
        hostPort: 30092
        protocol: TCP
      - containerPort: 30093
        hostPort: 30093
        protocol: TCP
      - containerPort: 30095
        hostPort: 30095
        protocol: TCP
      - containerPort: 30096
        hostPort: 30096
        protocol: TCP
      - containerPort: 30097
        hostPort: 30097
        protocol: TCP
      - containerPort: 30084
        hostPort: 30084
        protocol: TCP
      - containerPort: 30080
        hostPort: 30080
        protocol: TCP
      - containerPort: 30081
        hostPort: 30081
        protocol: TCP
      - containerPort: 30090
        hostPort: 30090
        protocol: TCP
      - containerPort: 30098
        hostPort: 30098
        protocol: TCP
      - containerPort: 30099
        protocol: TCP
      - containerPort: 30100
        hostPort: 30100
        protocol: TCP
      - containerPort: 30101
        hostPort: 30101
        protocol: TCP
      - containerPort: 30102
        hostPort: 30102
        protocol: TCP
      - containerPort: 30103
        hostPort: 30103
        protocol: TCP
      - containerPort: 30104
        hostPort: 30104
        protocol: TCP
      - containerPort: 30105
        hostPort: 30105
        protocol: TCP
      - containerPort: 30106
        hostPort: 30106
        protocol: TCP
      - containerPort: 30107
        hostPort: 30107
        protocol: TCP
      - containerPort: 30109
        hostPort: 30109
        protocol: TCP
      - containerPort: 30110
        hostPort: 30110
        protocol: TCP
      - containerPort: 30111
        hostPort: 30111
        protocol: TCP
      - containerPort: 30112
        hostPort: 30112
        protocol: TCP
      - containerPort: 30113
        hostPort: 30113
        protocol: TCP
      - containerPort: 30114
        hostPort: 30114
        protocol: TCP
      - containerPort: 30115
        hostPort: 30115
        protocol: TCP
      - containerPort: 30116
        hostPort: 30116
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
        containerPath: /var/run/docker.sock
</code></pre>

<hr/>

<h2>🚀 Create the Cluster</h2>
<pre><code>kind create cluster --config cluster.yaml</code></pre>

<p>This will create a cluster named <code>demo</code> with the specified configuration.</p>

<hr/>

<h2>✅ Verify Cluster Creation</h2>
<ul>
  <li><code>kubectl get nodes</code> → Check nodes are Ready</li>
  <li><code>kubectl get pods -A</code> → View all running pods</li>
  <li><code>kubectl cluster-info</code> → View cluster access URLs</li>
</ul>

<hr/>

<h2>📦 Basic kind Commands</h2>
<table border="1" cellpadding="6" cellspacing="0">
<thead>
<tr><th>Task</th><th>Command</th><th>Purpose</th></tr>
</thead>
<tbody>
<tr><td>Create Cluster</td><td><code>kind create cluster --config cluster.yaml</code></td><td>Creates a cluster with specified config</td></tr>
<tr><td>Delete Cluster</td><td><code>kind delete cluster --name demo</code></td><td>Deletes the demo cluster</td></tr>
<tr><td>List Clusters</td><td><code>kind get clusters</code></td><td>Shows all existing kind clusters</td></tr>
<tr><td>Load Docker Image</td><td><code>kind load docker-image image-name:tag --name demo</code></td><td>Injects local Docker image into kind cluster</td></tr>
</tbody>
</table>

<hr/>

<h2>🧭 kubectl Context Management</h2>
<table border="1" cellpadding="6" cellspacing="0">
<thead>
<tr><th>Task</th><th>Command</th><th>Purpose</th></tr>
</thead>
<tbody>
<tr><td>View contexts</td><td><code>kubectl config get-contexts</code></td><td>Lists available kubeconfig contexts</td></tr>
<tr><td>Switch context</td><td><code>kubectl config use-context kind-demo</code></td><td>Switches to the kind demo context</td></tr>
<tr><td>Delete context</td><td><code>kubectl config delete-context kind-demo</code></td><td>Removes the context</td></tr>
<tr><td>Test connection</td><td><code>kubectl get nodes</code></td><td>Verifies connection to cluster</td></tr>
</tbody>
</table>

<p><strong>📘 Repository:</strong> <a href="https://github.com/praveen581348/cluster" target="_blank">https://github.com/praveen581348/cluster</a></p>

</div>
<h2>📚 Resources</h2>
<ol>
  <!-- GitHub Repos & Overviews -->
  <li>📦 <a href="https://github.com/praveen581348/project_allinone" target="_blank">GitHub: project_allinone</a></li>
   <li>🔁 <a href="https://github.com/praveen581348/project_allinone/blob/master/application_flow.md" target="_blank">Application Flow (GitHub)</a></li>
  <li>📋 <a href="https://github.com/praveen581348/project_allinone/blob/master/SDLC-and-DevOps-Overview.md" target="_blank">SDLC & DevOps Overview</a></li>
  
  <!-- Docker, Kubernetes, kind -->
  <li>🚀 <a href="https://github.com/praveen581348/project_allinone/blob/master/why_docker_kubernetes_kind.md" target="_blank">Why Docker, Kubernetes & kind?</a></li>
  <li>🔧 <a href="https://github.com/praveen581348/project_allinone/blob/master/why_docker_kubernetes_kind.md" target="_blank">Setup Kind Cluster</a></li>
  <li>🌐 <a href="https://github.com/praveen581348/cluster" target="_blank">Cluster Repository</a></li>
  
  <!-- Docker -->
  <li>🐳 <a href="https://chatgpt.com/share/6857d18a-a8c0-8001-9c67-850a90e9ddbe" target="_blank">Learn Docker (ChatGPT)</a></li>
  
  <!-- Kubernetes -->
  <li>☸️ <a href="https://chatgpt.com/share/6857e648-5de0-8001-ab14-7897f0aa5989" target="_blank">Learn Kubernetes (ChatGPT)</a></li>
  
  <!-- kind -->
  <li>🧪 <a href="https://chatgpt.com/share/6857e7f1-2d24-8001-88c5-41d0bf8c0c51" target="_blank">Learn kind Cluster (ChatGPT)</a></li>
  
  <!-- Spring Boot + Maven -->
  <li>🛠️ <a href="https://github.com/praveen581348/project_allinone/blob/master/why_springboot_maven.md" target="_blank">Why Spring Boot + Maven?</a></li>
  <li>🌱 <a href="https://chatgpt.com/share/685854c4-f9b4-8001-a16d-bab5320f29d5" target="_blank">Spring Boot Notes & Concepts (ChatGPT)</a></li>
  <li>📘 <a href="https://chatgpt.com/share/6859922a-e6f4-8001-864e-ba59b47ad706" target="_blank">Maven Notes (ChatGPT)</a></li>
  
  <!-- Kafka + ZooKeeper -->
  <li>📡 <a href="https://github.com/praveen581348/project_allinone/blob/master/setup_kafka_zookpeer.md" target="_blank">Setup Kafka & ZooKeeper (GitHub)</a></li>
  <li>📄 <a href="https://chatgpt.com/share/685d3b2e-485c-8001-bc5c-8c3702594e35" target="_blank">Kafka & ZooKeeper Concepts & Architecture (ChatGPT)</a></li>
  <li>📂 <a href="https://github.com/praveen581348/kafka_zookeeper" target="_blank">Kafka & ZooKeeper Repository</a></li>

   <!-- SenderService -->
   <li>🚀 <a href="https://github.com/praveen581348/project_allinone/blob/master/create_senderservice.md" target="_blank">Create SenderService – Spring Boot Kafka Producer</a></li>
   <li>📁 <a href="https://github.com/praveen581348/senderservice" target="_blank">SenderService Git Repository</a></li>
    <li>📦 <a href="https://github.com/praveen581348/project_allinone/blob/master/run_senderservice_as_pod.md" target="_blank">Run SenderService as a Pod (Kubernetes Deployment Guide)</a></li>
    <li>✅ <a href="https://github.com/praveen581348/project_allinone/blob/master/verify_senderservice_kafka.md" target="_blank">Verify SenderService Producing to Kafka</a></li>

    <!-- MySQL -->
  <li>🗄️ <a href="github.com/praveen581348/project_allinone/blob/master/setup_mysql.md" target="_blank">Setup MySQL User Guide</a></li>
  <li>💾 <a href="https://github.com/praveen581348/mysql" target="_blank">MySQL Repository</a></li>

  <!-- Redis -->
  <li>⚡ <a href="https://github.com/praveen581348/project_allinone/blob/master/what_is_Redis.md" target="_blank">What is Redis?</a></li>
  <li>🔴 <a href="https://github.com/praveen581348/project_allinone/blob/master/setup_redis_guide.md" target="_blank">Setup Redis Guide</a></li>
  <li>📚 <a href="https://github.com/praveen581348/redis" target="_blank">Redis Repository</a></li>

  <!-- MongoDB -->
  <li>🍃 <a href="https://github.com/praveen581348/project_allinone/blob/master/setup_mongodb.md" target="_blank">MongoDB Setup Guide</a></li>
  <li>🧩 <a href="https://github.com/praveen581348/mongodb" target="_blank">MongoDB Repository</a></li>


</ol>