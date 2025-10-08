<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#2F80ED;">
  💾 MongoDB Deployment: Our NoSQL Data Store
</h1>
<p align="center">
  <img src="mongodb_arch.png" alt="MongoDB Architecture" width="600"/>
</p>
<p align="center" style="font-size:18px; color:#444;">
  A detailed guide to deploying a MongoDB database in a local Kubernetes cluster, configured for our project's data flow.
</p>

<hr style="border:1px solid #ddd;"/>

<h2><img src="https://www.mongodb.com/assets/images/global/leaf.png" alt="MongoDB Logo" height="25" style="vertical-align: middle;"/> What is MongoDB?</h2>
<p>
  <strong>MongoDB</strong> is a popular open-source NoSQL database that stores data in flexible, JSON-like documents. It is a critical component for modern, distributed applications that require dynamic schemas and high scalability. For our project, MongoDB will serve as the final data store for our analytical pipeline.
</p>

<h3>Key Concepts</h3>
<p>Understanding these concepts is crucial for managing our NoSQL database effectively.</p>
<ul>
  <li><strong>Documents & Collections:</strong> MongoDB stores data in <strong>documents</strong> (BSON objects), which are grouped into <strong>collections</strong> (similar to tables in MySQL).</li>
  <li><strong>Flexible Schema:</strong> Unlike relational databases, documents in a collection can have different fields, which is ideal for storing varied data from our <code>Data Service</code>.</li>
  <li><strong>Horizontal Scaling:</strong> MongoDB's design allows for easy horizontal scaling (sharding), distributing data across multiple servers to handle massive data loads.</li>
</ul>

<hr style="border:1px dashed #ccc;"/>

<h2>⚙️ Deployment on Kubernetes (kind)</h2>
<p>We'll deploy MongoDB using a standard <strong>Deployment</strong> for the pod and a <strong>Service</strong> to ensure network connectivity. We will place all our database-related resources in a dedicated <code>database</code> namespace.</p>

<h3>1. Kubernetes Manifests</h3>
<p>These files define all the necessary resources for our MongoDB deployment. For our development environment, we will use an <code>emptyDir</code> volume, which is not persistent and is recreated with the pod. A production environment would use a <code>PersistentVolumeClaim</code>.</p>

<h4><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/39/Kubernetes_logo_without_workmark.svg/723px-Kubernetes_logo_without_workmark.svg.png" alt="Kubernetes Icon" height="18" style="vertical-align: middle;"/> mongodb-manifests.yaml</h4>
<pre><code>apiVersion: v1
kind: Namespace
metadata:
  name: database
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb
  namespace: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
        - name: mongodb
          image: mongo:7
          ports:
            - containerPort: 27017
          volumeMounts:
            - name: mongo-storage
              mountPath: /data/db
      volumes:
        - name: mongo-storage
          emptyDir: {}
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb
  namespace: database
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
</code></pre>
<p>
 
  The <strong>Deployment</strong> ensures that our MongoDB pod is running, and the <strong>Service</strong> provides a stable DNS entry (<code>mongodb.database.svc.cluster.local</code>) for other services to connect to.
</p>

<hr style="border:1px dashed #ccc;"/>

<h2>✅ Configuration and Verification</h2>
<p>After applying the YAML files, you can connect to the MongoDB instance to verify its functionality and prepare it for use by our application.</p>
<h3>1. Apply the Manifests</h3>
<pre><code>kubectl apply -f mongodb-manifests.yaml</code></pre>

<h3>2. Verify the Deployment</h3>
<pre><code>kubectl get all -n database</code></pre>
<p>
  You should see your Deployment, Pod, and Service all in a Running or Ready state.
</p>

<h3>3. Access the Mongo Shell and Test the Database</h3>
<p>Execute a command to log in to the database from your terminal and test that the database is ready.</p>
<pre><code># Get the MongoDB Pod Name
MONGO_POD_NAME=$(kubectl get pods -n database -l app=mongodb -o jsonpath='{.items[0].metadata.name}')

# Access the mongo shell
kubectl exec -it -n database $MONGO_POD_NAME -- mongosh</code></pre>

<p>Once inside the Mongo shell, run the following commands to create a test collection:</p>
<pre><code>use crud;
db.submitted_messages.insertOne({ status: "test-complete" });
db.submitted_messages.find({});</code></pre>
<p>
 
  These commands will create a database called <code>crud</code> and insert a test document into a collection called <code>submitted_messages</code>, confirming that MongoDB is ready for use by your <code>Data Service</code>.
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