<h1>📘 End-to-End DevOps Project Documentation (Stage 1)</h1>

<p>This document details the complete workflow for deploying a microservices architecture on Kubernetes, including the infrastructure setup, application development, CI/CD pipelines, and observability stack.</p>

<h2>🏗️ 1. Infrastructure Setup (Stage 1.1)</h2>

<p>We establish a local Kubernetes environment using Kind (Kubernetes in Docker) to simulate a production-like cluster.</p>

<p><strong>Cluster Tool:</strong> Kind (kind)</p>

<p><strong>Configuration:</strong> Custom cluster.yaml defining a multi-node setup (1 Control Plane, 2 Workers).</p>

<h3>Networking:</h3>

<p>
<strong>Extra Port Mappings:</strong> Exposed host ports for all critical services (Jenkins: 30080, Nexus: 30081, ArgoCD: 30101, Prometheus: 30104, Grafana: 30107, Application ports).
</p>

<p>
<strong>Persistence:</strong> extraMounts configured to map a host directory (~/jenkins-data) to the node container (/mnt/data/jenkins) to ensure Jenkins data survives cluster restarts.
</p>

<h2>🧩 2. Microservices Architecture (Stage 1.2)</h2>

<p>The application is a distributed system designed to demonstrate asynchronous messaging, persistence, and caching.</p>

<h3>A. Sender Service (Spring Boot)</h3>

<p>
<strong>Role:</strong> Producer / Ingress.<br/>
<strong>Function:</strong> Hosts a web UI where users input a message_id and content.<br/>
<strong>Flow:</strong> Publishes the data to the Kafka topic <em>test-topic</em>.
</p>

<h3>B. Messaging Layer (Kafka & Zookeeper)</h3>

<p>
<strong>Role:</strong> Message Broker.<br/>
<strong>Function:</strong> Decouples the Sender and Receiver services. Ensures reliable, asynchronous message delivery.
</p>

<h3>C. Receiver Service (Spring Boot)</h3>

<p>
<strong>Role:</strong> Consumer / Processor.<br/>
<strong>Function:</strong> Listens to test-topic, consumes messages, and displays them on a dashboard.<br/>
<strong>Action:</strong> User triggers "Save to MySQL," persisting the record.
</p>

<h3>D. Data Service (Python/Flask)</h3>

<p>
<strong>Role:</strong> Query & Archival.<br/>
<strong>Function:</strong> Provides a search interface.<br/>
<strong>Smart Caching:</strong> Checks Redis first. If missing, falls back to MySQL.<br/>
<strong>Archival:</strong> Submits verified records to MongoDB for long-term storage.
</p>

<h3>E. Data Stores</h3>

<p>
<strong>MySQL:</strong> Primary transactional database (System of Record).<br/>
<strong>Redis:</strong> In-memory cache for high-speed read access.<br/>
<strong>MongoDB:</strong> NoSQL document store for historical/analytical data.
</p>

<h2>🛠️ 3. CI/CD Toolchain Setup (Stage 1.3 - 1.7)</h2>

<p>We deployed a robust set of DevOps tools within the platform namespace to manage the lifecycle.</p>

<h3>Nexus Repository Manager (Stage 1.3)</h3>

<p>
<strong>Role:</strong> Artifact Storage.<br/>
<strong>Setup:</strong> Hosted Maven repositories (sender, receiver) created to store build JARs.<br/>
<strong>Integration:</strong> Configured in pom.xml (distributionManagement) and Jenkins (settings.xml).
</p>

<h3>SonarQube (Stage 1.5)</h3>

<p>
<strong>Role:</strong> Static Code Analysis.<br/>
<strong>Function:</strong> Scans source code for bugs, vulnerabilities, and code smells.<br/>
<strong>Integration:</strong> Configured in Jenkins via the SonarQube Scanner plugin.
</p>

<h3>Docker Hub (Stage 1.7)</h3>

<p>
<strong>Role:</strong> Container Registry.<br/>
<strong>Function:</strong> Stores the production-ready Docker images (praveen581348/senderservice:&lt;tag&gt;).
</p>

<h2>🚀 4. Continuous Integration (Jenkins) (Stage 1.8)</h2>

<p>We implemented two distinct CI strategies to demonstrate different Jenkins capabilities.</p>

<h3>A. Sender Service (Freestyle Job)</h3>

<p>
<strong>Type:</strong> Freestyle Project.<br/>
<strong>Purpose:</strong> Learning the fundamental build steps.
</p>

<p><strong>Workflow:</strong></p>

<ul>
<li><strong>SCM:</strong> Polls GitHub (*/master) every 5 minutes.</li>
<li><strong>Build:</strong> Runs mvn clean deploy to push the JAR to Nexus.</li>
<li><strong>Code Quality:</strong> Runs SonarQube analysis.</li>
<li><strong>Package:</strong> Builds Docker image using a dynamic tag (${BUILD_NUMBER}).</li>
<li><strong>Publish:</strong> Pushes the image to Docker Hub.</li>
<li><strong>Deploy:</strong> Uses helm upgrade to update the application in the sender namespace.</li>
</ul>

<h3>B. Receiver Service (Pipeline Job)</h3>

<p>
<strong>Type:</strong> Declarative Pipeline (Jenkinsfile).<br/>
<strong>Purpose:</strong> Modern "Pipeline-as-Code" implementation.
</p>

<p><strong>Workflow:</strong></p>

<ul>
<li>Checkout: Clones the repo.</li>
<li>Build & Push: Uses configFileProvider to inject Nexus credentials securely for Maven deploy.</li>
<li>Docker: Builds and pushes the image.</li>
<li>Deploy: Uses kubectl set image to perform a rolling update on the existing Deployment.</li>
</ul>

<h2>📈 5. Observability (Prometheus & Grafana) (Stage 1.9)</h2>

<p>We implemented a full monitoring stack to visualize cluster and application health.</p>

<h3>Prometheus (Data Collection):</h3>

<p>
<strong>Deployment:</strong> Used kube-prometheus-stack Helm chart.<br/>
<strong>Configuration:</strong><br/>
- Storage Disabled (enabled: false) to use ephemeral storage for Kind.<br/>
- ServiceMonitor created to scrape /actuator/prometheus (Java) and /metrics (Python).
</p>

<p><strong>Fixes:</strong></p>

<ul>
<li>Connection Refused: Fixed by setting SERVER_ADDRESS=0.0.0.0 in app deployments.</li>
<li>Missing Targets: Fixed by ensuring Service ports were named http and labels matched the ServiceMonitor selector.</li>
</ul>

<h3>Grafana (Visualization):</h3>

<p>
<strong>Access:</strong> Exposed via NodePort 30107.<br/>
<strong>Data Source:</strong> Connected to Prometheus internal ClusterIP.<br/>
<strong>Dashboards:</strong> Created panels for Request Rate (RPS) and imported community dashboards for Node/Cluster health.
</p>

<h2>📦 6. GitOps Preparation (Stage 1.10 - 1.11)</h2>

<p>To prepare for advanced CD, we restructured the configuration management.</p>

<p><strong>Repository Separation:</strong></p>

<ul>
<li><strong>App Repos:</strong> senderservice, receiverservice (Code + Dockerfile).</li>
<li><strong>Config Repo:</strong> k8s-manifests (Holds Helm values/YAMLs for Dev/Prod).</li>
</ul>

<h3>ArgoCD (Continuous Deployment):</h3>

<p>
<strong>Role:</strong> GitOps controller.<br/>
<strong>Function:</strong> Watches the k8s-manifests repo. When Jenkins updates the image tag in the config repo, ArgoCD automatically syncs the change to the cluster.
</p>

<h2>📝 Summary</h2>

<p>
This Stage 1 completion marks the transition from a manual, local setup to a fully automated, observable, and scalable DevOps Platform. You have successfully integrated:
</p>

<ul>
<li>Code: Java/Python Microservices.</li>
<li>Build: Maven & Docker.</li>
<li>Quality: SonarQube.</li>
<li>Artifacts: Nexus & Docker Hub.</li>
<li>CI: Jenkins (Freestyle & Pipeline).</li>
<li>CD: Helm & ArgoCD.</li>
<li>Monitoring: Prometheus & Grafana.</li>
</ul>

<p>You are now ready to proceed to Stage 2, creating the dedicated Platform and Development clusters.</p>
