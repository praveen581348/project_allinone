<h1>📘 Step 1: Infrastructure Setup Notes (Multi-Cluster Kind)</h1>

<p>This is an excellent start to your multi-cluster journey! You are defining a very clear boundary between your <strong>Platform</strong> (management tools) and your <strong>Workload</strong> (application environments: Dev & Prod). This "Hub-and-Spoke" architecture is a standard practice for scalable and secure Kubernetes deployments.</p>

<p>Here is a detailed guide and set of notes for <strong>Step 1: Infrastructure Setup</strong>, covering the creation of your three Kind clusters, persistent storage configuration, and how to manage multiple cluster contexts with <code>kubectl</code>.</p>

<hr/>

<h2>1. Architectural Overview</h2>

<p>We are establishing a three-cluster topology to separate concerns:</p>

<ul>
  <li><strong>Platform Cluster (<code>platform</code>):</strong> The central management plane. It hosts shared DevOps tools (Jenkins, Nexus, SonarQube, etc.) and is not exposed to application traffic.</li>
  <li><strong>Development Cluster (<code>dev</code>):</strong> The target for active development and integration testing. It runs the latest versions of your microservices and databases.</li>
  <li><strong>Production Cluster (<code>prd</code>):</strong> The stable, live environment. It hosts the production-ready versions of your services.</li>
</ul>

<h3>Key Configuration Strategies</h3>

<ul>
  <li><strong>Host Port Mapping:</strong> Each cluster has distinct <code>hostPort</code> mappings in its control plane node, allowing access from your host without conflicts.</li>
</ul>

<p>Examples:</p>

<ul>
  <li><strong>Platform:</strong> 30080, 30081, 30101, etc.</li>
  <li><strong>Dev:</strong> 30084, 30098, etc.</li>
  <li><strong>Prod:</strong> 32000, 32001, etc.</li>
</ul>

<ul>
  <li><strong>Persistent Storage (extraMounts):</strong> Each cluster mounts a dedicated WSL directory to ensure data survival across restarts.</li>
</ul>

<p>Examples:</p>

<ul>
  <li>Platform → <code>~/platform-data</code> → <code>/mnt/data</code></li>
  <li>Dev → <code>~/application_data</code> → <code>/mnt/app-data</code></li>
  <li>Prod → <code>~/prd_application_data</code> → <code>/mnt/app-data</code></li>
</ul>

<hr/>

<h2>📁 Git Repository for Kind Cluster YAML Files</h2>

<p>All cluster configuration YAML files (<code>platform_cluster.yaml</code>, <code>dev_cluster.yaml</code>, <code>prd_Cluster.yaml</code>) are stored in the following repository:</p>

<p>
  🔗 <a href="https://github.com/praveen581348/cluster" target="_blank">
  https://github.com/praveen581348/cluster
  </a>
</p>

<p>Clone this repository before creating clusters:</p>

<pre><code>git clone https://github.com/praveen581348/cluster
cd cluster
</code></pre>

<hr/>

<h2>2. Setup Commands</h2>

<p>Run these commands sequentially in your WSL terminal.</p>

<h3>2.1 Prepare Persistent Storage Directories</h3>

<pre><code># --- 1. PLATFORM DATA (Jenkins, Nexus, SonarQube, etc.) ---
mkdir -p ~/platform-data/{grafana,jenkins,nexus,prometheus,sonarqube}
sudo chmod -R 777 ~/platform-data

# --- 2. DEV APPLICATION DATA ---
mkdir -p ~/application_data/{mysql,mongodb,redis,kafka,zookeeper,rabbitmq,sender,receiver}
sudo chmod -R 777 ~/application_data

# --- 3. PROD APPLICATION DATA ---
mkdir -p ~/prd_application_data/{mysql,mongodb,redis,kafka,zookeeper,rabbitmq,sender,receiver}
sudo chmod -R 777 ~/prd_application_data

echo "✅ Persistent storage directories created and configured."
</code></pre>

<h3>2.2 Create the Clusters</h3>

<p>Make sure you run these from the directory containing <code>platform_cluster.yaml</code>, <code>dev_cluster.yaml</code>, and <code>prd_Cluster.yaml</code>.</p>

<pre><code># --- 1. CREATE PLATFORM CLUSTER ---
kind create cluster --config platform_cluster.yaml --name platform

# --- 2. CREATE DEV CLUSTER ---
kind create cluster --config dev_cluster.yaml --name dev

# --- 3. CREATE PROD CLUSTER ---
kind create cluster --config prd_Cluster.yaml --name prd

echo "✅ All three clusters (platform, dev, prd) have been successfully created."
</code></pre>

<hr/>

<h2>3. Managing Multiple Clusters with kubectl</h2>

<p>Your <code>~/.kube/config</code> file stores authentication details, cluster API endpoints, and contexts. When you create a Kind cluster, Kind automatically adds:</p>

<ul>
  <li>A new <strong>cluster</strong> entry</li>
  <li>A new <strong>user</strong> entry</li>
  <li>A new <strong>context</strong> entry</li>
  <li>Sets the new cluster as the <strong>current-context</strong></li>
</ul>

<h3>3.1 View All Contexts</h3>

<pre><code>kubectl config get-contexts
</code></pre>

<h3>3.2 Switch to Platform Cluster</h3>

<pre><code>kubectl config use-context kind-platform
kubectl get nodes
</code></pre>

<h3>Switch to Dev Cluster</h3>

<pre><code>kubectl config use-context kind-dev
kubectl get nodes
</code></pre>

<h3>Switch to Prod Cluster</h3>

<pre><code>kubectl config use-context kind-prd
kubectl get nodes
</code></pre>

<p><strong>Pro Tip:</strong> Install <code>kubectx</code> for faster context switching.</p>

<hr/>

<h2>✅ Summary of Step 1</h2>

<ul>
  <li>Designed a secure, scalable multi-cluster architecture</li>
  <li>Prepared host-based persistent storage directories</li>
  <li>Created <code>platform</code>, <code>dev</code>, and <code>prd</code> clusters with Kind</li>
  <li>Learned how to manage contexts using <code>kubectl</code></li>
  <li>Added GitHub repo for YAML configuration files</li>
</ul>


