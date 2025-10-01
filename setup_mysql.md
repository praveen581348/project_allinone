<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#2F80ED;">
  🚀 Deploying MySQL on Kind with Persistent Storage
</h1>

<p align="center" style="font-size:18px; color:#444;">
  A detailed guide to deploying a MySQL database in a local Kubernetes cluster, ensuring data persistence and network connectivity.
</p>

<hr style="border:1px solid #ddd;"/>

<h2><img src="https://www.mysql.com/common/logos/mysql-logo-95x49.png" alt="MySQL Logo" height="25" style="vertical-align: middle;"/> What is MySQL?</h2>
<p>
  <strong>MySQL</strong> is a popular open-source relational database management system (RDBMS) used for storing structured data. It is a critical component for any application that requires data integrity and consistency, making it the perfect choice for our authoritative system of record.
</p>

<h3>Key Concepts</h3>
<p>Understanding these concepts is crucial for managing your database effectively in a microservices environment.</p>
<ul>
  <li><strong>Connection String:</strong> The unique address an application uses to connect to the database. In Kubernetes, this uses a DNS entry provided by the Service. For example: <code>jdbc:mysql://mysql.database.svc.cluster.local:3306/crud</code>.</li>
  <li><strong>Users & Privileges:</strong> An application should connect with a dedicated user that has specific permissions (privileges) to access and manipulate data. This follows the principle of least privilege, enhancing security.</li>
</ul>

<h3>Basic SQL Commands</h3>
<p>Here are the fundamental SQL commands for managing data. </p>
<ul>
  <li><strong><code>CREATE</code>:</strong> Creates a new database or a table.</li>
  <li><strong><code>INSERT</code>:</strong> Adds new rows of data into a table.</li>
  <li><strong><code>SELECT</code>:</strong> Retrieves data from a database.</li>
  <li><strong><code>UPDATE</code>:</strong> Modifies existing data in a table.</li>
  <li><strong><code>DELETE</code>:</strong> Removes rows of data from a table.</li>
</ul>

<hr style="border:1px dashed #ccc;"/>

<h2>⚙️ Deployment on Kubernetes (kind)</h2>
<p>We'll deploy MySQL using a <strong>Deployment</strong> for the pod and a <strong>Persistent Volume Claim (PVC)</strong> to ensure that our data is durable and won't be lost if the pod restarts or is moved.</p>

<h3>1. Kubernetes Manifests</h3>
<p>We will define our deployment with a dedicated namespace, a PVC, and a combined manifest for the deployment and service.</p>

<h4><img src="https://kubernetes.io/images/favicon.png" alt="Kubernetes Icon" height="18" style="vertical-align: middle;"/> mysql-manifests.yaml</h4> <p>This file contains all the necessary resources for our deployment. It's a best practice to keep related resources in a single manifest for easier management.</p> <pre><code>apiVersion: v1 kind: Namespace metadata:   name: database
apiVersion: v1 kind: PersistentVolumeClaim metadata:   name: mysql-pvc   namespace: database spec:   accessModes:     - ReadWriteOnce   resources:     requests:       storage: 1Gi
apiVersion: apps/v1 kind: Deployment metadata:   name: mysql   namespace: database   labels:     app: mysql spec:   replicas: 1   selector:     matchLabels:       app: mysql   template:     metadata:       labels:         app: mysql     spec:       containers:         - name: mysql           image: mysql:8.0           ports:             - containerPort: 3306           env:             - name: MYSQL_ROOT_PASSWORD               value: "password"           volumeMounts:             - name: mysql-storage               mountPath: /var/lib/mysql       volumes:         - name: mysql-storage           persistentVolumeClaim:             claimName: mysql-pvc
apiVersion: v1
kind: Service
metadata:
  name: mysql
  namespace: database
spec:
  ports:
    - port: 3306
      targetPort: 3306
  selector:
    app: mysql
  clusterIP: None
</code></pre>
<p>
 
  The <strong>PersistentVolumeClaim</strong> ensures that your MySQL data is stored on a persistent disk, which is automatically provisioned by Kind. The <strong>Service</strong> is a headless service (<code>clusterIP: None</code>) that provides a stable DNS entry for your ReceiverService to connect to.
</p>

<hr style="border:1px dashed #ccc;"/>

<h2>✅ Configuration and Verification</h2>
<p>After applying the YAML files, you can connect to the database to configure it for your application's use.</p>
<h3>1. Apply the Manifests</h3>
<pre><code>kubectl apply -f mysql-manifests.yaml</code></pre>

<h3>2. Verify the Deployment</h3>
<pre><code>kubectl get all -n database</code></pre>
<p>
  You should see your Deployment, Pod, Service, and PersistentVolumeClaim all in a Running or Bound state.
</p>

<h3>3. Access the MySQL CLI and Configure the Database</h3>
<p>Execute a command to log in to the database from your terminal and set up the schema for your project.</p>
<pre><code># Get the MySQL Pod Name
MYSQL_POD_NAME=$(kubectl get pods -n database -l app=mysql -o jsonpath='{.items[0].metadata.name}')

Access MySQL CLI and run your SQL commands
kubectl exec -it -n database $MYSQL_POD_NAME -- mysql -u root -p'password'</code></pre>

<p>Once inside the MySQL prompt, run the following commands:</p>
<pre><code>CREATE USER 'receiveruser'@'%' IDENTIFIED BY 'receiverpassword';
CREATE DATABASE crud;
GRANT ALL PRIVILEGES ON crud.* TO 'receiveruser'@'%';
USE crud;
CREATE TABLE kafka_message (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    content VARCHAR(255),
    received_at DATETIME(6)
);
EXIT;</code></pre>
<p>
 
  These commands create a dedicated user and a kafka_message table, which your ReceiverService will use to store messages received from Kafka.
</p>

</div>

---

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
    <li>📦 <a href="https://github.com/praveen581348/project_allinone/blob/master/verify_senderservice_kafka.md" target="_blank">Verify SenderService Integration with Kafka</a></li>

 <!-- DataBase -->
   <li>🚀 <a href="https://github.com/praveen581348/project_allinone/blob/master/create_senderservice.md" target="_blank">What's MySQL & Setup MySQL</a></li>
   <li>📁 <a href="https://github.com/praveen581348/senderservice" target="_blank">What's MongoDB & Setup MySQL</a></li>
    <li>📦 <a href="https://github.com/praveen581348/project_allinone/blob/master/run_senderservice_as_pod.md" target="_blank">What's Redis</a></li>
    <li>📦 <a href="https://github.com/praveen581348/project_allinone/blob/master/run_senderservice_as_pod.md" target="_blank">Setup Redis</a></li>
</ol>





