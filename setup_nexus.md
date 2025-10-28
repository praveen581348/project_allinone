<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#E67E22; border-bottom: 3px solid #E67E22; padding-bottom: 10px;">
    ⚙️ Deploying Nexus Repository on Kubernetes (Phase 1.9)
</h1>

<p align="center" style="font-size:18px; color:#444;">
    This configuration deploys the **Sonatype Nexus Repository Manager (Nexus 3 OSS)** to our Kind cluster, establishing our central artifact warehouse for the CI/CD pipeline.
</p>

<hr style="border:1px solid #ddd;"/>

<h2>1. Kubernetes Manifests (<code>nexus-manifests.yaml</code>)</h2>

<p>
    The deployment is split into three resources for separation: a Namespace, a Deployment for the application pod, and a NodePort Service for external access.
</p>

<h3>A. Namespace Isolation (`artifactory`)</h3>
<pre><code>apiVersion: v1
kind: Namespace
metadata:
  name: artifactory
</code></pre>
<p><strong>Purpose:</strong> Creates the <code>artifactory</code> namespace to isolate the repository manager from other application and database workloads.</p>

<h3>B. Nexus Deployment (`nexus-deployment`)</h3>
<pre><code>apiVersion: apps/v1
kind: Deployment
metadata:
  name: nexus-deployment
  namespace: artifactory
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nexus
  template:
    metadata:
      labels:
        app: nexus
    spec:
      containers:
        - name: nexus
          image: sonatype/nexus3:latest
          ports:
            - containerPort: 8081
          env:
            - name: NEXUS_DATA
              value: /nexus-data
          volumeMounts:
            - name: nexus-data
              mountPath: /nexus-data
      volumes:
        - name: nexus-data
          emptyDir: {}
</code></pre>
<p>
    <strong>Resource:</strong> <code>Deployment</code><br/>
    <strong>Data Storage (Crucial Note):</strong> We use an <strong><code>emptyDir</code></strong> volume for the <code>/nexus-data</code> directory. This is standard and acceptable for a local **Kind testing environment** but must be replaced by a `PersistentVolumeClaim` for production deployment to avoid data loss.
</p>

<h3>C. Nexus Service (`nexus-service`)</h3>
<pre><code>apiVersion: v1
kind: Service
metadata:
  name: nexus-service
  namespace: artifactory
spec:
  type: NodePort
  selector:
    app: nexus
  ports:
    - port: 8081
      targetPort: 8081
      nodePort: 30081
</code></pre>
<p>
    <strong>Resource:</strong> <code>Service</code><br/>
    <strong>Mapping:</strong> The <code>NodePort: 30081</code> exposes the internal container port <code>8081</code> to the host machine. This allows you to access the Nexus Web UI via <code>http://localhost:30081/</code>.
</p>

<hr style="border:1px dashed #ccc;"/>

<h2>2. Next Steps After Deployment</h2>

<h3>🚀 Applying the Manifests</h3>
<p>
    Save all resources into a single YAML file (e.g., <code>nexus-manifests.yaml</code>) and apply them:
</p>
<pre><code>kubectl apply -f nexus-manifests.yaml</code></pre>

<h3>🔍 Initial Setup Checklist</h3>
<ol>
    <li><strong>Wait for Startup:</strong> Nexus is a large Java application and takes several minutes to initialize. Monitor the Pod status and logs.</li>
    <li><strong>Retrieve Initial Password:</strong> Once the Pod is running, retrieve the temporary admin password:
        <pre><code>kubectl exec -it &lt;nexus-pod-name&gt; -n artifactory -- cat /nexus-data/admin.password</code></pre>
    </li>
    <li><strong>Web UI Configuration:</strong> Access the Nexus Web UI at <code>http://localhost:30081/</code>, log in, change the password, and configure your first repositories (Maven Hosted/Proxy, Docker Hosted) for the CI/CD pipeline.</li>
</ol>

</div>
<hr/>
<hr/>
<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#E67E22; border-bottom: 3px solid #E67E22; padding-bottom: 10px;">
    🚀 Deploying Maven Artifacts to Nexus
</h1>

<p align="center" style="font-size:18px; color:#444;">
    This guide configures your local environment to deploy <code>senderservice</code> and <code>receiverservice</code> artifacts to the Kubernetes-hosted Nexus Repository Manager.
</p>

<hr style="border:1px solid #ddd;"/>

<h2>1. Prerequisites and Repository Setup</h2>

<p>
    Before deploying, confirm that Nexus is running via its NodePort and that you have created the necessary Maven repositories.
</p>

<ul>
    <li><strong>Nexus Access Point:</strong> <code>http://&lt;Your Mac IP&gt;:30081/</code></li>
    <li><strong>Repositories Needed:</strong> You must create two separate <strong>Maven2 (Hosted)</strong> repositories in Nexus: <code>sender</code> and <code>receiver</code>.</li>
</ul>

<hr style="border:1px dashed #ccc;"/>

<h2>2. Authentication: The Role of <code>~/.m2/settings.xml</code></h2>

<p>
    Maven uses this file to authenticate deployment requests. The <code>&lt;id&gt;</code> tags defined here must match the IDs used in your projects' <code>pom.xml</code> files.
</p>

<h3>Configuration (`~/.m2/settings.xml`)</h3>
<pre><code>&lt;settings&gt;
  &lt;servers&gt;
    &lt;server&gt;
      &lt;id&gt;sender&lt;/id&gt;
      &lt;username&gt;admin&lt;/username&gt;
      &lt;password&gt;admin&lt;/password&gt;
    &lt;/server&gt;
    &lt;server&gt;
      &lt;id&gt;receiver&lt;/id&gt;
      &lt;username&gt;admin&lt;/username&gt;
      &lt;password&gt;admin&lt;/password&gt;
    &lt;/server&gt;
  &lt;/servers&gt;
&lt;/settings&gt;
</code></pre>
<p>
    <strong>⚠️ Security Note:</strong> For CI/CD (Jenkins), use deployment-only credentials and store them securely, not in plain text.
</p>

<hr style="border:1px dashed #ccc;"/>

<h2>3. Configuration: The Role of <code>pom.xml</code></h2>

<p>
    The <code>&lt;distributionManagement&gt;</code> section in your POM tells Maven the exact Nexus URL to deploy the artifact to, linking it via the established <code>&lt;id&gt;</code>.
</p>

<h3>🔹 <code>senderservice/pom.xml</code></h3>
<pre><code>&lt;distributionManagement&gt;
  &lt;repository&gt;
    &lt;id&gt;sender&lt;/id&gt;
    &lt;name&gt;Sender Nexus Repository&lt;/name&gt;
    &lt;url&gt;http://192.168.1.23:30081/repository/sender/&lt;/url&gt;
  &lt;/repository&gt;
  &lt;snapshotRepository&gt;
    &lt;id&gt;sender&lt;/id&gt;
    &lt;name&gt;Sender Nexus Snapshot Repository&lt;/name&gt;
    &lt;url&gt;http://192.168.1.23:30081/repository/sender/&lt;/url&gt;
  &lt;/snapshotRepository&gt;
&lt;/distributionManagement&gt;
</code></pre>

<h3>🔸 <code>receiverservice/pom.xml</code></h3>
<pre><code>&lt;distributionManagement&gt;
  &lt;repository&gt;
    &lt;id&gt;receiver&lt;/id&gt;
    &lt;name&gt;Receiver Nexus Repository&lt;/name&gt;
    &lt;url&gt;http://192.168.1.23:30081/repository/receiver/&lt;/url&gt;
  &lt;/repository&gt;
  &lt;snapshotRepository&gt;
    &lt;id&gt;receiver&lt;/id&gt;
    &lt;name&gt;Receiver Nexus Snapshot Repository&lt;/name&gt;
    &lt;url&gt;http://192.168.1.23:30081/repository/receiver/&lt;/url&gt;
  &lt;/snapshotRepository&gt;
&lt;/distributionManagement&gt;
</code></pre>

<hr style="border:1px dashed #ccc;"/>

<h2>4. Execution and Verification</h2>

<h3>🚀 Deploy Command</h3>
<p>
    Navigate to the root directory of each respective service and run the Maven <code>deploy</code> goal.
</p>
<pre><code>mvn clean deploy -DskipTests</code></pre>

<h3>✅ Verification</h3>
<p>
    Once the command completes successfully, verify the artifacts are correctly uploaded:
</p>
<ol>
    <li>The console output shows **<code>BUILD SUCCESS</code>**.</li>
    <li>Browse the Nexus Web UI (<code>http://192.168.1.23:30081/</code>) and inspect the **<code>sender</code>** and **<code>receiver</code>** repositories. The service JAR files should be listed, confirming the CI deployment path is functional.</li>
</ol>
<p>This deployment validates the crucial first step of artifact management before full CI pipeline automation. </p>

</div>