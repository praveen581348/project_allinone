<div>

<h1>📘 Step 1: Multi-Cluster Infrastructure Setup Notes</h1>

<h2>1. Architectural Overview: The Hub-and-Spoke Model</h2>
<p align="center">
  <img src="step1.png"  width="600"/>
</p>
<p>
We are establishing a sophisticated four-cluster topology running locally on Kind (Kubernetes in Docker). 
This "Hub-and-Spoke" architecture separates concerns, ensures security, and mimics a real enterprise production lifecycle.
</p>

<h3>The Four Clusters</h3>

<table>
<thead>
<tr>
<th>Cluster Name</th>
<th>Role & Purpose</th>
<th>Key Characteristics</th>
</tr>
</thead>
<tbody>
<tr>
<td><b>Platform</b> (Hub)</td>
<td>Management Plane – Central orchestrator hosting DevOps tools</td>
<td>Hosts Jenkins, Nexus, Prometheus, Grafana</td>
</tr>
<tr>
<td><b>Dev</b> (Spoke)</td>
<td>Active development cluster for rapid iterations</td>
<td>High churn, ephemeral databases</td>
</tr>
<tr>
<td><b>QA</b> (Spoke)</td>
<td>Quality Assurance, Security Scanning</td>
<td>Hosts SonarQube, Trivy; mimics Prod setup</td>
</tr>
<tr>
<td><b>Prod</b> (Spoke)</td>
<td>Stable environment serving end users</td>
<td>Runs production-ready builds</td>
</tr>
</tbody>
</table>

<hr/>

<h2>2. Key Configuration Strategies</h2>

<h3>A. Distinct Host Port Mapping</h3>

<p>Each cluster receives its own non-overlapping port range:</p>

<ul>
<li><b>Platform:</b> 30080–30100</li>
<li><b>Dev:</b> 30200–30300</li>
<li><b>QA:</b> 31000–31200</li>
<li><b>Prod:</b> 32000–32200</li>
</ul>

<h3>B. Persistent Storage (extraMounts)</h3>

<p>Each cluster mounts a unique host directory:</p>

<ul>
<li>Platform → <code>~/platform-data</code></li>
<li>Dev → <code>~/dev_application_data</code></li>
<li>QA → <code>~/qa_data</code></li>
<li>Prod → <code>~/prd_application_data</code></li>
</ul>

<hr/>

<h2>3. Cluster Configuration Files (YAML)</h2>

<h3>3.1 Platform Cluster (<code>platform_cluster.yaml</code>)</h3>

<pre>
<code>
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: platform
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30080
        hostPort: 30080
      - containerPort: 30081
        hostPort: 30081
      - containerPort: 30090
        hostPort: 30090
      - containerPort: 30104
        hostPort: 30104
      - containerPort: 30107
        hostPort: 30107
    extraMounts:
      - hostPath: /var/run/docker.sock
        containerPath: /var/run/docker.sock
      - hostPath: ~/platform-data
        containerPath: /mnt/data
  - role: worker
    extraMounts:
      - hostPath: ~/platform-data
        containerPath: /mnt/data
  - role: worker
    extraMounts:
      - hostPath: ~/platform-data
        containerPath: /mnt/data
</code>
</pre>

<h3>3.2 Dev Cluster (<code>dev_cluster.yaml</code>)</h3>

<pre>
<code>
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: dev
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30084
        hostPort: 30084
      - containerPort: 30098
        hostPort: 30098
      - containerPort: 30113
        hostPort: 30113
    extraMounts:
      - hostPath: ~/dev_application_data
        containerPath: /mnt/app-data
  - role: worker
    extraMounts:
      - hostPath: ~/dev_application_data
        containerPath: /mnt/app-data
  - role: worker
    extraMounts:
      - hostPath: ~/dev_application_data
        containerPath: /mnt/app-data
</code>
</pre>

<h3>3.3 QA Cluster (<code>qa_cluster.yaml</code>)</h3>

<pre>
<code>
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: qa
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 31000
        hostPort: 31000
      - containerPort: 31084
        hostPort: 31084
      - containerPort: 31098
        hostPort: 31098
    extraMounts:
      - hostPath: ~/qa_data
        containerPath: /mnt/qa-data
  - role: worker
    extraMounts:
      - hostPath: ~/qa_data
        containerPath: /mnt/qa-data
  - role: worker
    extraMounts:
      - hostPath: ~/qa_data
        containerPath: /mnt/qa-data
</code>
</pre>

<h3>3.4 Prod Cluster (<code>prd_cluster.yaml</code>)</h3>

<pre>
<code>
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: prd
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 32000
        hostPort: 32000
      - containerPort: 32001
        hostPort: 32001
      - containerPort: 32007
        hostPort: 32007
    extraMounts:
      - hostPath: ~/prd_application_data
        containerPath: /mnt/app-data
  - role: worker
    extraMounts:
      - hostPath: ~/prd_application_data
        containerPath: /mnt/app-data
  - role: worker
    extraMounts:
      - hostPath: ~/prd_application_data
        containerPath: /mnt/app-data
</code>
</pre>

<hr/>

<h2>4. Setup Execution Steps</h2>

<h3>4.1 Prerequisites</h3>
<p>Ensure Docker Desktop or Docker Engine is running and Kind is installed.</p>

<h3>4.2 Prepare Host Storage</h3>

<pre>
<code>
mkdir -p ~/platform-data/{grafana,jenkins,nexus,prometheus} && sudo chmod -R 777 ~/platform-data
mkdir -p ~/dev_application_data/{mysql,redis,kafka,zookeeper,rabbitmq} && sudo chmod -R 777 ~/dev_application_data
mkdir -p ~/qa_data/{sonarqube_data,sonarqube_logs,trivy_cache} && sudo chmod -R 777 ~/qa_data
mkdir -p ~/prd_application_data/{mysql,redis,kafka,zookeeper,rabbitmq} && sudo chmod -R 777 ~/prd_application_data
</code>
</pre>

<h3>4.3 Create the Clusters</h3>

<pre>
<code>
kind create cluster --config platform_cluster.yaml --name platform
kind create cluster --config dev_cluster.yaml --name dev
kind create cluster --config qa_cluster.yaml --name qa
kind create cluster --config prd_cluster.yaml --name prd
</code>
</pre>

<hr/>

<h2>5. Managing kubeconfig Contexts</h2>

<pre>
<code>
kubectl config get-contexts
kubectl config use-context kind-platform
kubectl config use-context kind-qa
kubectl get nodes
</code>
</pre>

<hr/>

<h2>📚 Learning Resources</h2>

<ul>
<li><a href="https://www.docker.com/get-started/">Docker – Get Started</a></li>
<li><a href="https://kubernetes.io/docs/tutorials/kubernetes-basics/">Kubernetes Basics</a></li>
<li><a href="https://kind.sigs.k8s.io/docs/user/quick-start/">Kind Quick Start Guide</a></li>
</ul>

</div>
