<h1>📦 Run SenderService as a POD (Spring Boot Kafka Producer)</h1>

<p>
In this step, we deploy our first microservice – <strong>SenderService</strong> – as a <strong>Pod</strong> inside a Kubernetes cluster. This guide covers building the application, creating a Docker image, and deploying the service on Kubernetes using kind.
</p>

<hr/>

<h2>🧱 Step 1: Build the Application with Maven</h2>

<h3>🔍 What is Maven?</h3>
<ul>
  <li>Compiles source code</li>
  <li>Manages dependencies</li>
  <li>Runs unit tests</li>
  <li>Packages the app as a JAR</li>
</ul>

<h3>🔧 Build Commands</h3>
<pre><code>cd path/to/senderservice/
mvn clean package -DskipTests</code></pre>

<p><strong>Output:</strong></p>
<pre><code>target/senderservice-0.0.1-SNAPSHOT.jar</code></pre>

<hr/>

<h2>🐳 Step 2: Create Docker Image & Push to Docker Hub</h2>

<h3>📁 Dockerfile</h3>
<pre><code>FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8098
ENTRYPOINT ["java", "-jar", "app.jar"]</code></pre>

<h3>🔨 Build and Push</h3>
<pre><code># Build the image
docker build -t praveen581348/senderservice:latest .

# Push to Docker Hub
docker push praveen581348/senderservice:latest</code></pre>

<blockquote>
  ⚠️ Replace <code>praveen581348</code> with your Docker Hub username.<br/>
  ✅ In future steps, we will move to a private registry like <strong>Harbor</strong>.
</blockquote>

<hr/>

<h2>☸️ Step 3: Kubernetes Deployment Resources</h2>

<p>Save all the below YAMLs in a single file named <code>senderservice.yaml</code>.</p>

<h3>📂 A. Namespace</h3>
<pre><code>apiVersion: v1
kind: Namespace
metadata:
  name: sender</code></pre>

<h3>🚀 B. Deployment</h3>
<pre><code>apiVersion: apps/v1
kind: Deployment
metadata:
  name: senderservice-deployment
  namespace: sender
  labels:
    app: senderservice
spec:
  replicas: 1
  selector:
    matchLabels:
      app: senderservice
  template:
    metadata:
      labels:
        app: senderservice
    spec:
      containers:
        - name: senderservice
          image: praveen581348/senderservice:latest
          ports:
            - containerPort: 8098
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: "kubernetes"
            - name: SPRING_KAFKA_BOOTSTRAP_SERVERS
              value: "kafka.messaging.svc.cluster.local:9092"</code></pre>

<h3>🌐 C. Service</h3>
<pre><code>apiVersion: v1
kind: Service
metadata:
  name: senderservice-service
  namespace: sender
spec:
  type: NodePort
  selector:
    app: senderservice
  ports:
    - port: 8098
      targetPort: 8098
      nodePort: 30098</code></pre>

<p><strong>Access URL:</strong> <code>http://localhost:30098/</code> (in kind)</p>

<hr/>

<h2>🧪 Step 4: Apply YAMLs & Verify</h2>

<pre><code>kubectl apply -f senderservice.yaml
kubectl get all -n sender</code></pre>

<hr/>

<h2>🧾 Summary Table</h2>
<table border="1" cellspacing="0" cellpadding="6">
<tr><th>Resource</th><th>Purpose</th></tr>
<tr><td>Namespace</td><td>Isolates senderservice from other workloads</td></tr>
<tr><td>Deployment</td><td>Runs and manages pods for the Spring Boot app</td></tr>
<tr><td>Service</td><td>Exposes app via NodePort to outside world</td></tr>
<tr><td>Dockerfile</td><td>Packages the Spring Boot app into a minimal Java image</td></tr>
<tr><td>Docker Image</td><td>Pushed to Docker Hub for deployment</td></tr>
</table>

<hr/>

<h2>🔗 Related Resources</h2>
<ul>
  <li>🚀 <a href="https://github.com/praveen581348/project_allinone/blob/master/senderservice_readme.md" target="_blank">SenderService – Spring Boot Kafka Producer</a></li>
  <li>📁 <a href="https://github.com/praveen581348/senderservice" target="_blank">SenderService Git Repository</a></li>
  <li>🧰 <a href="https://github.com/praveen581348/project_allinone" target="_blank">Main Repository – project_allinone</a></li>
</ul>

<hr/>

<h2>📌 What’s Next?</h2>
<ul>
  <li>Use Harbor as a private container registry 🛡️</li>
  <li>Configure metrics with Prometheus 📊</li>
  <li>Forward logs to Splunk via OpenTelemetry 🪵</li>
  <li>Integrate with Redis, MySQL, and ReceiverService 🔄</li>
</ul>

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

</ol>
