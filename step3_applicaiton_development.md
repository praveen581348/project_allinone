<h1>📘 Step 3: Application Deployment Documentation (Dev, QA, & Prod)</h1>

<h2>1. Overview</h2>
<p>
In this step, we deploy the complete microservices application stack into three separate clusters:
<strong>Development (dev)</strong>, <strong>Quality Assurance (qa)</strong>, and <strong>Production (prd)</strong>.
<br><br>
This application demonstrates an asynchronous, event-driven architecture using Kafka for messaging, Redis for caching,
and both MySQL and MongoDB for persistence.
</p>

<h3>1.1 Application Workflow</h3>
<ol>
  <li>User submits a message via the Sender Service web UI.</li>
  <li>Sender Service publishes the message to a Kafka topic.</li>
  <li>Receiver Service consumes the message from Kafka and shows it on a dashboard.</li>
  <li>User clicks <em>Save to MySQL</em>, persisting the message in MySQL.</li>
  <li>User clicks <em>Cache to Redis</em>, loading data from MySQL into Redis.</li>
  <li>User queries the message ID via the Data Service.</li>
  <li>Data Service checks Redis (cache hit); if not found, queries MySQL (cache miss).</li>
  <li>User archives the final output into MongoDB.</li>
</ol>

<h2>2. Prerequisites</h2>
<ul>
  <li><strong>Step 1 & Step 2 Completed:</strong> Infrastructure and platform clusters must be running.</li>
  <li><strong>Git Repos Cloned:</strong> All required repositories should be cloned locally.</li>
  <li><strong>Branching:</strong> Each repo must contain the branches <code>dev</code>, <code>qa</code>, <code>master</code>.</li>
</ul>

<table>
<thead>
<tr><th>Service</th><th>Git Repository</th></tr>
</thead>
<tbody>
<tr><td>Sender Service</td><td>https://github.com/praveen581348/senderservice.git</td></tr>
<tr><td>Receiver Service</td><td>https://github.com/praveen581348/receiverservice.git</td></tr>
<tr><td>Data Service</td><td>https://github.com/praveen581348/dataservice.git</td></tr>
<tr><td>Kafka & Zookeeper</td><td>https://github.com/praveen581348/kafka_zookeeper.git</td></tr>
<tr><td>Redis</td><td>https://github.com/praveen581348/redis.git</td></tr>
<tr><td>MySQL</td><td>https://github.com/praveen581348/mysql.git</td></tr>
<tr><td>MongoDB</td><td>https://github.com/praveen581348/mongodb.git</td></tr>
</tbody>
</table>

<h2>3. Deployment Strategy (Dev, QA, Prod)</h2>
<p>We deploy the same application in all three clusters. Only the Kubernetes context and NodePorts differ.</p>

<table>
<thead>
<tr>
  <th>Environment</th>
  <th>Cluster Context</th>
  <th>NodePorts (Receiver / Sender / Data)</th>
</tr>
</thead>
<tbody>
<tr><td>Dev</td><td>kind-dev</td><td>30084 / 30098 / 30103</td></tr>
<tr><td>QA</td><td>kind-qa</td><td>32020 / 32021 / 32022</td></tr>
<tr><td>Prod</td><td>kind-prd</td><td>32000 / 32001 / 32002</td></tr>
</tbody>
</table>

<h2>4. Deployment Instructions</h2>
<p>Repeat these steps for <strong>dev</strong>, <strong>qa</strong>, and <strong>prod</strong>.</p>

<h3>⚠️ Switch Kubernetes Context First</h3>

<pre>
# Dev
kubectl config use-context kind-dev

# QA
kubectl config use-context kind-qa

# Prod
kubectl config use-context kind-prd
</pre>

<h3>4.1 Deploy Infrastructure Services (Data Layer)</h3>

<h4>1. Kafka & Zookeeper</h4>
<pre>
cd kafka_zookeeper
# git checkout dev | qa | master

kubectl apply -f zookeeper-deployment.yaml
kubectl apply -f zookeeper-service.yaml

kubectl apply -f kafka-deployment.yaml
kubectl apply -f kafka-service.yaml
</pre>

<h4>2. MySQL</h4>
<pre>
cd ../mysql
# git checkout dev | qa | master

kubectl apply -f mysql-pvc.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml
</pre>

<h4>3. Redis</h4>
<pre>
cd ../redis
kubectl apply -f redis-deployment.yaml
kubectl apply -f redis-service.yaml
</pre>

<h4>4. MongoDB</h4>
<pre>
cd ../mongodb
kubectl apply -f mongodb-pvc.yaml
kubectl apply -f mongodb-deployment.yaml
kubectl apply -f mongodb-service.yaml
</pre>

<p><strong>Verification:</strong></p>
<pre>
kubectl get pods
</pre>

<h3>4.2 Deploy Microservices (Application Layer)</h3>

<h4>1. Sender Service</h4>
<pre>
cd ../senderservice
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
</pre>

<h4>2. Receiver Service</h4>
<pre>
cd ../receiverservice
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
</pre>

<h4>3. Data Service</h4>
<pre>
cd ../dataservice
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
</pre>

<h2>5. Accessing the Applications</h2>

<table>
<thead>
<tr>
  <th>Service</th>
  <th>Dev URL</th>
  <th>QA URL</th>
  <th>Prod URL</th>
</tr>
</thead>
<tbody>
<tr>
  <td>Sender Service</td>
  <td>http://localhost:30098</td>
  <td>http://localhost:32021</td>
  <td>http://localhost:32001</td>
</tr>
<tr>
  <td>Receiver Service</td>
  <td>http://localhost:30084</td>
  <td>http://localhost:32020</td>
  <td>http://localhost:32000</td>
</tr>
<tr>
  <td>Data Service</td>
  <td>http://localhost:30103</td>
  <td>http://localhost:32022</td>
  <td>http://localhost:32002</td>
</tr>
</tbody>
</table>

<h2>6. Summary</h2>
<p>
You have successfully deployed a complete, event-driven microservices application into three isolated clusters—Dev, QA, and Prod.
The system now supports:
</p>

<ul>
  <li><strong>Kafka</strong> → asynchronous message transfer</li>
  <li><strong>Redis</strong> → caching for fast reads</li>
  <li><strong>MySQL</strong> → transactional storage</li>
  <li><strong>MongoDB</strong> → archival and history storage</li>
  <li><strong>Sender, Receiver, Data Services</strong> → fully operational across all environments</li>
</ul>
