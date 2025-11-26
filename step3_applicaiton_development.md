<h1>📘 Step 3: Application Deployment Documentation (Dev & Prod)</h1>

<h2>1. Overview</h2>

<p>In this step, we will deploy the complete microservices application stack into both the <strong>Development (<code>dev</code>)</strong> and <strong>Production (<code>prd</code>)</strong> clusters. This application demonstrates an asynchronous, event-driven architecture using Kafka for messaging, Redis for caching, and both MySQL and MongoDB for persistence.</p>

<h3>1.1 Application Workflow</h3>

<ol>
  <li><strong>User</strong> submits a message via the <strong>Sender Service</strong> web UI.</li>
  <li><strong>Sender Service</strong> publishes the message to a <strong>Kafka</strong> topic.</li>
  <li><strong>Receiver Service</strong> consumes the message from Kafka and shows it on a dashboard.</li>
  <li>User clicks <em>"Save to MySQL"</em>, persisting the message in <strong>MySQL</strong>.</li>
  <li>User clicks <em>"Cache to Redis"</em>, loading data from MySQL into <strong>Redis</strong> for fast access.</li>
  <li>User queries the message ID via the <strong>Data Service</strong>.</li>
  <li><strong>Data Service</strong> checks Redis (cache hit). If not found, queries <strong>MySQL</strong> (cache miss).</li>
  <li>User archives the final output into <strong>MongoDB</strong>.</li>
</ol>

<hr/>

<h2>2. Prerequisites</h2>

<ul>
  <li><strong>Step 1 & Step 2 Completed:</strong> Infrastructure + Platform clusters must be running.</li>
  <li><strong>Git Repos Cloned:</strong> You should have cloned all required repositories.</li>
  <li><strong>Branching:</strong> Each repo contains <code>master</code> (Prod) and <code>dev</code> (Dev) branches.</li>
</ul>

<table>
<thead>
<tr>
<th>Service</th>
<th>Git Repository</th>
</tr>
</thead>
<tbody>
<tr><td>Sender Service</td><td><code>https://github.com/praveen581348/senderservice.git</code></td></tr>
<tr><td>Receiver Service</td><td><code>https://github.com/praveen581348/receiverservice.git</code></td></tr>
<tr><td>Data Service</td><td><code>https://github.com/praveen581348/dataservice.git</code></td></tr>
<tr><td>Kafka & Zookeeper</td><td><code>https://github.com/praveen581348/kafka_zookeeper.git</code></td></tr>
<tr><td>Redis</td><td><code>https://github.com/praveen581348/redis.git</code></td></tr>
<tr><td>MySQL</td><td><code>https://github.com/praveen581348/mysql.git</code></td></tr>
<tr><td>MongoDB</td><td><code>https://github.com/praveen581348/mongodb.git</code></td></tr>
</tbody>
</table>

<hr/>

<h2>3. Deployment Strategy (Dev & Prod)</h2>

<p>We deploy <strong>the same application</strong> to both clusters. The only difference is the <strong>NodePorts</strong> used:</p>

<ul>
  <li><strong>Dev Cluster:</strong> Ports like <code>30098</code>, <code>30084</code>, <code>30103</code>.</li>
  <li><strong>Prod Cluster:</strong> Ports like <code>32001</code>, <code>32000</code>, <code>32002</code>.</li>
</ul>

<hr/>

<h2>4. Deployment Instructions</h2>

<p><strong>Repeat these steps for BOTH clusters: dev and prod.</strong>  
Before deploying, switch your kube context:</p>

<pre><code># To deploy into Dev:
kubectl config use-context kind-dev

# To deploy into Prod:
kubectl config use-context kind-prd
</code></pre>

<h3>4.1 Deploying Infrastructure Services (Data Layer)</h3>

<p>Deploy these first — all application services depend on them.</p>

<h4>1. Kafka & Zookeeper</h4>

<pre><code>cd kafka_zookeeper
git checkout dev    # Use 'master' for Prod

kubectl apply -f zookeeper-deployment.yaml
kubectl apply -f zookeeper-service.yaml

# Wait for Zookeeper to be running
kubectl apply -f kafka-deployment.yaml
kubectl apply -f kafka-service.yaml
</code></pre>

<h4>2. MySQL</h4>

<pre><code>cd ../mysql
git checkout dev    # Use 'master' for Prod

kubectl apply -f mysql-pvc.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f mysql-service.yaml
</code></pre>

<h4>3. Redis</h4>

<pre><code>cd ../redis
git checkout dev    # Use 'master' for Prod

kubectl apply -f redis-deployment.yaml
kubectl apply -f redis-service.yaml
</code></pre>

<h4>4. MongoDB</h4>

<pre><code>cd ../mongodb
git checkout dev    # Use 'master' for Prod

kubectl apply -f mongodb-pvc.yaml
kubectl apply -f mongodb-deployment.yaml
kubectl apply -f mongodb-service.yaml
</code></pre>

<p><strong>Verification:</strong></p>

<pre><code>kubectl get pods
</code></pre>

<p>Ensure all infrastructure pods are in <strong>Running</strong> state.</p>

<hr/>

<h3>4.2 Deploying Microservices (Application Layer)</h3>

<h4>1. Sender Service</h4>

<pre><code>cd ../senderservice
git checkout dev    # Use 'master' for Prod

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml   # NodePort differs for dev/prod
</code></pre>

<h4>2. Receiver Service</h4>

<pre><code>cd ../receiverservice
git checkout dev    # Use 'master' for Prod

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
</code></pre>

<h4>3. Data Service</h4>

<pre><code>cd ../dataservice
git checkout dev    # Use 'master' for Prod

kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
</code></pre>

<hr/>

<h2>5. Accessing the Applications</h2>

<p>After deployment, access both Dev and Prod environments simultaneously using the URLs below:</p>

<table>
<thead>
<tr>
<th>Service</th>
<th>Dev URL</th>
<th>Prod URL</th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>Sender Service</strong></td>
<td><code>http://localhost:30098</code></td>
<td><code>http://localhost:32001</code></td>
</tr>
<tr>
<td><strong>Receiver Service</strong></td>
<td><code>http://localhost:30084</code></td>
<td><code>http://localhost:32000</code></td>
</tr>
<tr>
<td><strong>Data Service</strong></td>
<td><code>http://localhost:30103</code></td>
<td><code>http://localhost:32002</code></td>
</tr>
</tbody>
</table>

<hr/>

<h2>6. Summary</h2>

<p>You have successfully deployed a complete, event-driven microservices application into <strong>two separate clusters</strong> — Dev and Prod.</p>

<ul>
  <li>Kafka handles async message transfers</li>
  <li>Redis accelerates read performance</li>
  <li>MySQL stores operational data</li>
  <li>MongoDB archives historical data</li>
  <li>Sender, Receiver, and Data Services are fully operational</li>
</ul>


