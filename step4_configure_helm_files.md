<h1>📘 Step 4: Configuring Helm Charts for Multiple Environments</h1>

<p>
In this step, we will configure our Helm charts to support different environments—specifically <strong>development (dev)</strong> and <strong>production (prod)</strong>.  
This structure allows us to maintain a single reusable Helm chart template while storing environment-specific overrides.
</p>

<hr/>

<h2>1. Create Dev and Prod Branches in GitHub</h2>

<p>For each application repository (<code>senderservice</code>, <code>receiverservice</code>, etc.), create <code>dev</code> and <code>prod</code> branches. This enables environment-specific code and configuration management.</p>

<h3>Steps:</h3>

<ol>
  <li>Navigate to your repository on GitHub.</li>
  <li>Click the <strong>"Branch: main"</strong> dropdown.</li>
  <li>Type <code>dev</code> in the search box → click <strong>"Create branch: dev from main"</strong>.</li>
  <li>Repeat the same for the <code>prod</code> branch.</li>
</ol>

<hr/>

<h2>2. Create Environment-Specific <code>values.yaml</code> Files</h2>

<p>Each application will have separate Helm value files for <strong>dev</strong> and <strong>prod</strong> inside the <code>helm/&lt;service-name&gt;-chart/environments/</code> directory.</p>

<h3>Standard Helm Chart Structure</h3>

<pre><code>helm/&lt;service-name&gt;-chart/
  ├── Chart.yaml
  ├── values.yaml            # Default values
  ├── templates/             # Kubernetes YAML templates
  └── environments/
        ├── dev/
        │    └── values.yaml
        └── prod/
             └── values.yaml
</code></pre>

<p>This structure helps override settings like:</p>

<ul>
  <li>Replica counts</li>
  <li>Image tags</li>
  <li>NodePorts</li>
  <li>Environment variables</li>
  <li>Resource limits</li>
</ul>

<hr/>

<h2>2.1 Configuring Helm for <code>senderservice</code></h2>

<h3>Development Environment Values</h3>

<pre><code>replicaCount: 1
image:
  repository: praveen581348/senderservice
  tag: dev
service:
  type: NodePort
  nodePort: 30098
env:
  - name: SPRING_PROFILES_ACTIVE
    value: "dev"
</code></pre>

<h3>Production Environment Values</h3>

<pre><code>replicaCount: 3
image:
  repository: praveen581348/senderservice
  tag: prod
service:
  type: NodePort
  nodePort: 32001
env:
  - name: SPRING_PROFILES_ACTIVE
    value: "prod"
resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 250m
    memory: 256Mi
</code></pre>

<hr/>

<h2>2.2 Configuring Helm for <code>receiverservice</code></h2>

<h3>Development Environment Values</h3>

<pre><code>replicaCount: 1
image:
  repository: praveen581348/receiverservice
  tag: dev
service:
  type: NodePort
  nodePort: 30084
env:
  - name: SPRING_PROFILES_ACTIVE
    value: "dev"
</code></pre>

<h3>Production Environment Values</h3>

<pre><code>replicaCount: 3
image:
  repository: praveen581348/receiverservice
  tag: prod
service:
  type: NodePort
  nodePort: 32000
env:
  - name: SPRING_PROFILES_ACTIVE
    value: "prod"
resources:
  limits:
    cpu: 500m
    memory: 512Mi
  requests:
    cpu: 250m
    memory: 256Mi
</code></pre>

<hr/>

<h2>3. Commit and Push Changes</h2>

<p>Once the <code>dev</code> and <code>prod</code> <code>values.yaml</code> files are created, commit them to the correct branches.</p>

<h3>Steps:</h3>

<ol>
  <li>Switch to the <code>dev</code> branch:</li>
</ol>

<pre><code>git checkout dev
</code></pre>

<ol start="2">
  <li>Add and commit files:</li>
</ol>

<pre><code>git add helm/&lt;service-name&gt;-chart/environments/dev/values.yaml
git commit -m "Add dev values.yaml"
git push origin dev
</code></pre>

<ol start="3">
  <li>Repeat the same for the <code>prod</code> branch:</li>
</ol>

<pre><code>git checkout prod
git add helm/&lt;service-name&gt;-chart/environments/prod/values.yaml
git commit -m "Add prod values.yaml"
git push origin prod
</code></pre>

<hr/>

<h2>✔ Summary</h2>

<ul>
  <li>Created <code>dev</code> and <code>prod</code> Git branches</li>
  <li>Structured Helm charts for multi-environment deployments</li>
  <li>Created environment-specific <code>values.yaml</code> files</li>
  <li>Configured replica counts, NodePorts, resource limits, and profile settings</li>
  <li>Prepared the foundation for automated environment-based Helm deployments via Jenkins</li>
</ul>


