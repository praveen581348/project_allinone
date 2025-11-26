<h1>📘 Step 2: Platform Cluster Setup Documentation</h1>

<h2>1. Overview</h2>

<p>In this step, we will populate the Platform Cluster with the essential DevOps tools that will manage our software lifecycle.</p>

<ul>
  <li><strong>Nexus Repository Manager:</strong> To store build artifacts (JARs) and proxy external libraries.</li>
  <li><strong>SonarQube:</strong> For continuous code quality inspection and static analysis.</li>
  <li><strong>Jenkins:</strong> The automation server for building, testing, and deploying applications.</li>
  <li><strong>Dekcer Hub:</strong> The external registry for storing production-ready container images.</li>
</ul>

<hr/>

<h2>2. Prerequisites</h2>

<ul>
  <li><strong>Step 1 Completed:</strong> The platform cluster must be running.</li>
  <li><strong>Context Switched:</strong> Ensure your kubectl context is set to the platform cluster:</li>
</ul>

<pre><code>kubectl config use-context kind-platform
</code></pre>

<ul>
  <li><strong>Git Repos Cloned:</strong> You should have cloned the repositories containing the Kubernetes manifests for each tool.</li>
</ul>

<hr/>

<h2>3. Tool Deployment & Configuration</h2>

<h3>3.1. Nexus Repository Manager</h3>

<p>
  <strong>Repository:</strong> 
  <a href="https://github.com/praveen581348/nexus.git" target="_blank">
    https://github.com/praveen581348/nexus.git
  </a>
</p>

<h4>Deployment</h4>

<p>Navigate to your cloned nexus directory and apply the manifests.</p>

<pre><code>kubectl apply -f namespace.yaml
kubectl apply -f pvc.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
</code></pre>

<p><strong>Verification:</strong></p>

<p>Wait for the pod to be Running and access Nexus at <code>http://localhost:30081</code>.</p>

<pre><code>kubectl get pods -n nexus
</code></pre>

<h4>Configuration (Post-Deployment)</h4>

<p><strong>Retrieve Initial Password:</strong></p>

<pre><code>kubectl exec -it -n nexus &lt;nexus-pod-name&gt; -- cat /nexus-data/admin.password
</code></pre>

<p><strong>Log In:</strong> Use <code>admin</code> and the retrieved password. Follow the setup wizard.</p>

<h4>Create Repositories:</h4>

<p>Go to <em>Server Administration</em> → <em>Repositories</em> → <em>Create repository</em>.</p>

<ul>
  <li><strong>Sender Repo</strong><br/>Recipe: maven2 (hosted)<br/>Name: sender<br/>Version policy: Mixed<br/>Deployment policy: Allow redeploy</li>
  <li><strong>Receiver Repo</strong><br/>Recipe: maven2 (hosted)<br/>Name: receiver<br/>Version policy: Mixed<br/>Deployment policy: Allow redeploy</li>
</ul>

<hr/>

<h3>3.2. SonarQube</h3>

<p>
  <strong>Repository:</strong>
  <a href="https://github.com/praveen581348/sonarqube.git" target="_blank">
    https://github.com/praveen581348/sonarqube.git
  </a>
</p>

<h4>Deployment</h4>

<p>Navigate to the sonar directory and apply the manifests (PostgreSQL + SonarQube).</p>

<pre><code># Example — adapt based on your structure
kubectl apply -f postgres-deployment.yaml
kubectl apply -f postgres-service.yaml
kubectl apply -f sonarqube-deployment.yaml
kubectl apply -f sonarqube-service.yaml
</code></pre>

<p><strong>Verification:</strong></p>

<p>Wait for the pods to be Running. Access SonarQube at <code>http://localhost:30090</code>.</p>

<pre><code>kubectl get pods -n sonarqube
</code></pre>

<p><strong>Initial Login:</strong> Default credentials → <code>admin / admin</code></p>

<hr/>

<h3>3.3. Jenkins</h3>

<p>
  <strong>Repository:</strong>
  <a href="https://github.com/praveen581348/jenkins.git" target="_blank">
    https://github.com/praveen581348/jenkins.git
  </a>
</p>

<h4>Deployment</h4>

<p>Navigate to the Jenkins repo and apply all manifests in one go:</p>

<pre><code>kubectl apply -f .
</code></pre>

<p><strong>Verification:</strong> Wait for pod Running, then open <code>http://localhost:30080</code>:</p>

<pre><code>kubectl get pods -n jenkins
</code></pre>

<h4>Configuration (Post-Deployment)</h4>

<p><strong>Retrieve Initial Admin Password:</strong></p>

<pre><code>kubectl exec -it -n jenkins &lt;jenkins-pod-name&gt; -- cat /var/jenkins_home/secrets/initialAdminPassword
</code></pre>

<p><strong>Unlock Jenkins:</strong> Enter the password in browser.</p>

<ul>
  <li>Install suggested plugins</li>
  <li>Create admin user</li>
</ul>

<h4>Configure Credentials</h4>

<p>Go to <em>Manage Jenkins → Credentials → Global</em>.</p>

<h5>Docker Hub:</h5>

<ul>
  <li>Kind: Username with password</li>
  <li>Username: <code>praveen581348</code></li>
  <li>Password: <code>&lt;your-dockerhub-password&gt;</code></li>
  <li>ID: <code>dockerhub-creds</code></li>
</ul>

<h5>Nexus:</h5>

<ul>
  <li>Kind: Username with password</li>
  <li>Username: <code>admin</code></li>
  <li>Password: <code>&lt;your-new-nexus-password&gt;</code></li>
  <li>ID: <code>nexus-creds</code></li>
</ul>

<hr/>

<h3>3.4. Docker Hub</h3>

<p>Ensure you have an account at <a href="https://hub.docker.com" target="_blank">Docker Hub</a>.</p>

<p>Create two public repositories:</p>

<ul>
  <li><code>senderservice</code></li>
  <li><code>receiverservice</code></li>
</ul>

<hr/>

<h2>4. Summary</h2>

<p>You have successfully set up the Platform Cluster with a complete DevOps toolchain:</p>

<ul>
  <li><strong>Jenkins:</strong> <code>http://localhost:30080</code></li>
  <li><strong>Nexus:</strong> <code>http://localhost:30081</code></li>
  <li><strong>SonarQube:</strong> <code>http://localhost:30090</code></li>
</ul>


