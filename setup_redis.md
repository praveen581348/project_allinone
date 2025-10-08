<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#DC3545; border-bottom: 3px solid #DC3545; padding-bottom: 10px;">
    🚀 Redis in Microservices and Kubernetes
</h1>

<p align="center" style="font-size:18px; color:#444;">
    A comprehensive guide to Redis—its role as a caching layer in our architecture—and the precise steps for its deployment and validation within our Kubernetes environment.
</p>

<hr style="border:1px solid #ddd;"/>

<h2>🔍 What is Redis?</h2>
<p>
    <strong>Redis (REmote DIctionary Server)</strong> is an <strong>open-source, in-memory data structure store</strong>. It serves primarily as a <strong>database</strong>, <strong>cache</strong>, and <strong>message broker</strong>, supporting structures like strings, hashes, lists, sets, and more.
</p>

<h3>Why Redis in Our Project?</h3>
<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #f0f0f0;">
            <th style="padding: 10px; border: 1px solid #ddd;">Feature</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Benefit</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>In-memory storage</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Extremely fast read/write</strong> (sub-millisecond) to solve latency issues.</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Caching (Cache-Aside)</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Reduces load on the primary <strong>MySQL</strong> database, improving performance for read-heavy operations.</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Replication</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Prepares the system for high availability and read scaling in future phases.</td>
        </tr>
    </tbody>
</table>

<hr style="border:1px solid #ddd;"/>

<h2>🛠️ Deployment on Kubernetes (Phase 1.4.2)</h2>
<p>
    We are deploying Redis into its own <strong><code>cache</code></strong> namespace using a lightweight <code>redis:7-alpine</code> image.
</p>

<h3>1. Kubernetes Manifests (<code>redis-manifests.yaml</code>)</h3>
<pre><code>apiVersion: v1
kind: Namespace
metadata:
  name: cache
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: cache
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:7-alpine
        ports:
        - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: redis
  namespace: cache
spec:
  selector:
    app: redis
  ports:
  - port: 6379
    targetPort: 6379
  clusterIP: None # Headless service for stable DNS
</code></pre>

<h3>2. Key Deployment Details</h3>
<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #f0f0f0;">
            <th style="padding: 10px; border: 1px solid #ddd;">Component</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Purpose</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Details</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Image</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Redis Server</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>redis:7-alpine</code> (A minimal, stable image)</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Port</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Redis Client Port</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>6379</code> (The default port)</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Service Name</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>DNS Discovery</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>redis.cache.svc.cluster.local</code></td>
        </tr>
    </tbody>
</table>

<h3>3. Application Connection Details</h3>
<p>
    The <code>ReceiverService</code> will use this configuration in its <code>application.properties</code> to connect automatically:
</p>
<pre><code>spring.data.redis.host=redis.cache.svc.cluster.local
spring.data.redis.port=6379</code></pre>

<hr style="border:1px solid #ddd;"/>

<h2>🧪 Validation Steps (Using Redis-CLI)</h2>

<p>After deploying, you must verify that Redis is up and running and ready to accept commands.</p>

<h3>1. Apply Manifests & Check Status</h3>
<pre><code>kubectl apply -f redis-manifests.yaml
kubectl get pods -n cache
</code></pre>

<h3>2. Access the Redis CLI and Test Connectivity</h3>
<pre><code># Get the Redis Pod Name
REDIS_POD_NAME=$(kubectl get pods -n cache -l app=redis -o jsonpath='{.items[0].metadata.name}')

# Execute the redis-cli inside the pod
kubectl exec -it -n cache $REDIS_POD_NAME -- redis-cli
</code></pre>

<h3>3. Run Test Commands (Inside the CLI)</h3>
<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #f0f0f0;">
            <th style="padding: 10px; border: 1px solid #ddd;">Command</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Purpose</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Expected Output</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong><code>PING</code></strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Checks server connectivity.</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong><code>PONG</code></strong></td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong><code>SET mykey "Hello Redis Cache"</code></strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Stores a simple key-value pair.</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong><code>OK</code></strong></td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong><code>GET mykey</code></strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Retrieves the stored value.</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong><code>"Hello Redis Cache"</code></strong></td>
        </tr>
    </tbody>
</table>

<p style="text-align: center; margin-top: 20px;">
    This successful validation confirms that your caching layer is ready for integration with your Spring Boot microservices.
</p>

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