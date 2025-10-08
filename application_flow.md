<h1>📘 Application Flow Documentation</h1>

<p>This document provides a comprehensive overview of the end-to-end architecture, flow, and interactions between different microservices and databases in the project.</p>

<h2>🧭 Architecture Diagram</h2>

<p>
  <img src="./app_flow.png" alt="Application Flow Diagram" width="100%" />
</p>

<h2>🚀 Explanation of Key Steps & Components</h2>

<h3>1. Sender Service (Spring Boot)</h3>
<ul>
  <li>Hosts a simple web form.</li>
  <li>User enters a <code>message_id</code> and <code>content</code> and clicks <strong>Send</strong>.</li>
  <li>This data is published to a Kafka topic: <code>test-topic</code>.</li>
</ul>

<h3>2. Kafka Broker (Pod)</h3>
<ul>
  <li>Acts as a message queue between producer (Sender Service) and consumer (Receiver Service).</li>
  <li>Ensures asynchronous communication and resilience.</li>
</ul>

<h3>3. Receiver Service (Spring Boot)</h3>
<ul>
  <li>Consumes messages from Kafka.</li>
  <li>Displays each message on a dashboard with an action to <strong>Save to MySQL</strong>.</li>
</ul>

<h3>4. MySQL (Pod)</h3>
<ul>
  <li>Stores messages persistently with fields: <code>id</code>, <code>content</code>, and <code>received_at</code>.</li>
  <li>Acts as the authoritative system of record.</li>
</ul>

<h3>5. Redis (Pod)</h3>
<ul>
  <li>Optimizes reads by acting as a caching layer.</li>
  <li>Stores entries from MySQL in format: <code>kafka:message:<id></code> → <code>content</code>.</li>
  <li>Used when "Save All MySQL Messages to Redis" is triggered.</li>
</ul>

<h3>6. Data Service (Python App)</h3>
<ul>
  <li>Dropdown interface displays all <code>message_ids</code> from MySQL.</li>
  <li>Upon selecting an ID and clicking <strong>Search</strong>:
    <ul>
      <li>It first checks Redis for the message.</li>
      <li>If not found, it falls back to MySQL.</li>
    </ul>
  </li>
  <li>Displays content and source (Redis or MySQL).</li>
  <li>On <strong>Submit</strong>, it stores the final data into MongoDB.</li>
</ul>

<h3>7. MongoDB (Pod)</h3>
<ul>
  <li>Stores all submitted messages with fields: <code>id</code>, <code>content</code>, <code>received_at</code>, and <code>source</code>.</li>
  <li>Acts as a historical and analytical data store.</li>
</ul>

<h2>🧩 Roles & Benefits of Each Component</h2>

<table border="1" cellpadding="10">
  <thead>
    <tr>
      <th>Component</th>
      <th>Role</th>
      <th>Benefits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Kafka</strong></td>
      <td>Message Queue</td>
      <td>Decouples sender & receiver, ensuring scalability and async communication.</td>
    </tr>
    <tr>
      <td><strong>MySQL</strong></td>
      <td>Primary Database</td>
      <td>Stores validated and received Kafka messages for persistence.</td>
    </tr>
    <tr>
      <td><strong>Redis</strong></td>
      <td>Caching Layer</td>
      <td>Improves read performance for frequent lookups by storing MySQL data.</td>
    </tr>
    <tr>
      <td><strong>MongoDB</strong></td>
      <td>Final Data Store</td>
      <td>Stores full user-submitted data with context for history or audits.</td>
    </tr>
    <tr>
      <td><strong>Search/Data Service</strong></td>
      <td>Lookup and Store Logic</td>
      <td>Implements intelligent caching by querying Redis first and falling back to MySQL.</td>
    </tr>
  </tbody>
</table>

<h2>📦 Summary</h2>

<ul>
  <li>This architecture demonstrates a modern microservice pattern combining message queues, caching, and persistent databases.</li>
  <li>Each layer improves modularity, performance, or traceability.</li>
  <li>The application is well-suited for analytical pipelines, dashboards, and user interaction-based message processing.</li>
</ul>

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