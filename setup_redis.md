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
