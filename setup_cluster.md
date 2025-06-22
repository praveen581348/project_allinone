<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif;">

<h1 align="center" style="color:#2F80ED;">⚙️ Setting Up Kubernetes Cluster Using kind</h1>

<p style="text-align:center; font-size:16px; color:#333;">
  A complete local development environment for deploying microservices using kind (Kubernetes IN Docker).
</p>

<hr style="border:1px solid #ddd;"/>

<h2 style="color:#27AE60;">🔧 What is kind?</h2>
<p><strong>kind</strong> stands for <em>Kubernetes IN Docker</em>. It lets you run a full Kubernetes cluster inside Docker containers, making it ideal for:</p>
<ul>
  <li>🔬 Local development and testing</li>
  <li>🔁 CI/CD workflows</li>
  <li>🎓 Learning Kubernetes hands-on</li>
</ul>

<hr/>

<h2 style="color:#F39C12;">💻 Setup on macOS (or Linux)</h2>
<ol>
  <li>
    <strong>Install Docker</strong>  
    <br/>Download & install: <a href="https://www.docker.com/products/docker-desktop" target="_blank">Docker Desktop</a>.  
    <em>Make sure Docker is running.</em>
  </li>
  <li>
    <strong>Install kind</strong>  
    <br/><code>brew install kind</code>
  </li>
  <li>
    <strong>Install kubectl</strong>  
    <br/><code>brew install kubectl</code>
  </li>
</ol>

<hr/>

<h2 style="color:#2980B9;">🚀 Creating the Cluster</h2>
<p>Create a cluster with custom ports using the following command:</p>
<pre><code>kind create cluster --config cluster.yaml</code></pre>

<p><strong>📄 File:</strong> <code>cluster.yaml</code> (stored in <a href="https://github.com/praveen581348/cluster" target="_blank">GitHub Repo</a>)</p>

<p>This file:</p>
<ul>
  <li>Creates 1 control-plane node and 2 worker nodes</li>
  <li>Maps container ports to host ports so services (Jenkins, Nexus, etc.) are accessible</li>
  <li>Mounts Docker socket to support inner-Docker builds</li>
</ul>

<hr/>

<h2 style="color:#9B59B6;">✅ Verifying Cluster Creation</h2>
<p>Once the cluster is created, you can verify it using:</p>
<pre><code>kubectl get nodes</code></pre>
<p>You should see output listing your control-plane and worker nodes in <code>Ready</code> status.</p>

<hr/>

<h2 style="color:#8E44AD;">📘 Basic kind Commands</h2>
<table border="1" cellpadding="8" cellspacing="0" style="border-collapse:collapse;">
<thead style="background-color:#f6f8fa;">
<tr><th>Task</th><th>Command</th><th>Description</th></tr>
</thead>
<tbody>
<tr><td>Create Cluster</td><td><code>kind create cluster --config cluster.yaml</code></td><td>Creates a kind cluster with specified configuration</td></tr>
<tr><td>Delete Cluster</td><td><code>kind delete cluster --name demo</code></td><td>Deletes the cluster named <code>demo</code></td></tr>
<tr><td>List Clusters</td><td><code>kind get clusters</code></td><td>Lists all clusters managed by kind</td></tr>
<tr><td>Load Image</td><td><code>kind load docker-image my-image:tag --name demo</code></td><td>Loads a locally built Docker image into the cluster</td></tr>
</tbody>
</table>

<hr/>

<h2 style="color:#34495E;">🧭 kubectl Context Management</h2>
<table border="1" cellpadding="8" cellspacing="0" style="border-collapse:collapse;">
<thead style="background-color:#f6f8fa;">
<tr><th>Task</th><th>Command</th><th>Description</th></tr>
</thead>
<tbody>
<tr><td>List Contexts</td><td><code>kubectl config get-contexts</code></td><td>Shows all available contexts</td></tr>
<tr><td>Use Context</td><td><code>kubectl config use-context kind-demo</code></td><td>Switches to the <code>kind-demo</code> cluster context</td></tr>
<tr><td>Delete Context</td><td><code>kubectl config delete-context kind-demo</code></td><td>Removes the context from kubeconfig</td></tr>
<tr><td>Test Connection</td><td><code>kubectl get nodes</code></td><td>Verifies if cluster is reachable</td></tr>
</tbody>
</table>

<hr/>

<h2 style="color:#2F80ED;">📁 GitHub Repository</h2>
<p>Find all the cluster setup files here:</p>
<p><strong>🔗 <a href="https://github.com/praveen581348/cluster" target="_blank">https://github.com/praveen581348/cluster</a></strong></p>

</div>
