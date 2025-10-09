<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#2F80ED; border-bottom: 3px solid #2F80ED; padding-bottom: 10px;">
    📦 Running the Receiver Service as a Pod
</h1>

<p align="center" style="font-size:18px; color:#444;">
    This guide details the final steps to deploy the <strong>Receiver Service</strong>—our Kafka consumer and data management microservice—to the Kubernetes cluster.
</p>

<hr style="border:1px solid #ddd;"/>

<h2>1. Build and Containerization</h2>
<p>
    The Spring Boot application is packaged and containerized to ensure a lightweight and consistent deployment environment.
</p>

<h3>🧱 Building with Maven</h3>
<pre><code># Navigate to the project root (where pom.xml is)
cd receiverservice

# Build the JAR package
mvn clean package -DskipTests
</code></pre>
<p>The executable JAR is generated at: <code>target/receiverservice-0.0.1-SNAPSHOT.jar</code></p>

<h3>🐳 Dockerizing the Application</h3>
<p>A minimal <strong>Dockerfile</strong> based on OpenJDK is used:</p>
<pre><code>FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8084
ENTRYPOINT ["java", "-jar", "app.jar"]</code></pre>

<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #f0f0f0;">
            <th style="padding: 10px; border: 1px solid #ddd;">Action</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Command</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Build Image</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>docker build -t praveen581348/receiverservice:21 .</code></td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Push Image</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>docker push praveen581348/receiverservice:21</code></td>
        </tr>
    </tbody>
</table>

<hr style="border:1px dashed #ccc;"/>

<h2>2. Kubernetes Deployment and Orchestration</h2>
<p>
    We deploy using a dedicated <strong>Namespace</strong>, a <strong>Deployment</strong> for the Pod, and a <strong>NodePort Service</strong> to expose the dashboard.
</p>

<h3>✅ Namespace (`receiver`)</h3>
<p>Isolates the microservice logically.</p>
<pre><code>apiVersion: v1
kind: Namespace
metadata:
  name: receiver
</code></pre>

<h3>☸️ Deployment (`receiverservice-deployment`)</h3>
<p>Runs the container and sets essential environment variables for **Kafka connection**.</p>
<pre><code>apiVersion: apps/v1
kind: Deployment
metadata:
  name: receiverservice-deployment
  namespace: receiver
  labels:
    app: receiverservice
spec:
  replicas: 1
  selector:
    matchLabels:
      app: receiverservice
  template:
    metadata:
      labels:
        app: receiverservice
    spec:
      containers:
        - name: receiverservice
          image: praveen581348/receiverservice:21
          ports:
            - containerPort: 8084
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: "kubernetes"
            - name: SPRING_KAFKA_BOOTSTRAP_SERVERS
              value: "kafka.messaging.svc.cluster.local:9092"
</code></pre>

<h3>🌐 Service (`receiverservice-service`)</h3>
<p>Uses a **NodePort** to expose the web dashboard to your host machine.</p>
<pre><code>apiVersion: v1
kind: Service
metadata:
  name: receiverservice-service
  namespace: receiver
spec:
  type: NodePort
  selector:
    app: receiverservice
  ports:
    - port: 8084
      targetPort: 8084
      nodePort: 30084
</code></pre>

<hr style="border:1px dashed #ccc;"/>

<h2>3. Deployment and Verification</h2>

<h3>🚀 Apply Manifests</h3>
<p>
    Apply the Kubernetes configuration files:
</p>
<pre><code>kubectl apply -f receiver-namespace.yaml
kubectl apply -f receiver-deployment.yaml
kubectl apply -f receiver-service.yaml
</code></pre>

<h3>🔍 Verification Steps</h3>
<ol>
    <li>**Check Pod Status:** Confirm the receiver pod is running.
        <pre><code>kubectl get pods -n receiver</code></pre>
    </li>
    <li>**View Logs:** Ensure the service successfully connected to **Kafka** and **MySQL** (logs should show connection initiation).
        <pre><code>kubectl logs -f deployment/receiverservice-deployment -n receiver</code></pre>
    </li>
    <li>**Access UI Dashboard:** Open the dashboard in your browser.
        <pre><code>Access URL: http://localhost:30084/messages</code></pre>
        This dashboard allows you to manually test the full data flow (Kafka consumption, MySQL persistence, and Redis caching).
    </li>
</ol>

</div>