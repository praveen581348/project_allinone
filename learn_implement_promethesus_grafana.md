<!-- 📈 Observability Stack: Prometheus & Grafana Notes -->

<h1>📈 Observability Stack: Prometheus &amp; Grafana Notes</h1>
<p>This document summarizes the core concepts, requirements, and architecture of the metrics stack for our microservices, ready for deployment.</p>

<hr>

<h2>🧐 I. Why Prometheus and Grafana are Required</h2>
<p>In a microservices architecture (<strong>Spring Boot, Kafka, MySQL</strong>, etc.), traditional monitoring (relying only on log files) is insufficient. We need a reliable system to quantify and understand the health of the entire distributed system.</p>

<table>
  <thead>
    <tr>
      <th>Tool</th>
      <th>Role in Observability</th>
      <th>Key Function</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Prometheus</strong></td>
      <td><strong>Collection &amp; Storage</strong> (The Brain)</td>
      <td>Aggregates, stores, and queries high-volume, <strong>time-series data</strong> from every running service.</td>
    </tr>
    <tr>
      <td><strong>Grafana</strong></td>
      <td><strong>Visualization &amp; Analytics</strong> (The Dashboard)</td>
      <td>Connects to Prometheus to transform raw metrics into insightful graphs, panels, and dashboards.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>🧠 II. Prometheus: Concepts, Pros, Cons, &amp; Configuration</h2>
<p>Prometheus is the core <strong>metrics collection and alerting</strong> toolkit.</p>

<h3>Basic Concepts</h3>
<ul>
  <li><strong>Time Series Data Model:</strong> Data is a timestamped value tied to a <strong>Metric Name</strong> and descriptive <strong>Labels</strong> (key-value identifiers).<br>
  <em>Example:</em> <code>http_requests_total{method="GET", app="sender-service"}</code></li>
  <li><strong>Pull Model (Scraping):</strong> Prometheus actively <strong>pulls</strong> metrics from configured endpoints (like <code>/actuator/prometheus</code>) on targets.</li>
  <li><strong>Service Discovery (SD):</strong> Dynamically finds targets (new Pods/Services) by querying the Kubernetes API server using labels.</li>
  <li><strong>PromQL:</strong> The powerful functional expression language used for querying and analysis.</li>
  <li><strong>Alertmanager:</strong> Handles <strong>alert grouping, routing, and silencing</strong>.</li>
</ul>

<h3>Advantages (Pros) ✅</h3>
<ul>
  <li><strong>Kubernetes Native:</strong> Excellent integration for dynamic discovery of targets.</li>
  <li><strong>Powerful PromQL:</strong> Enables complex analysis (rate, aggregation, prediction).</li>
  <li><strong>Open Source Standard:</strong> Massive community support.</li>
</ul>

<h3>Limitations (Cons) 🚫</h3>
<ul>
  <li><strong>Metrics Only:</strong> Cannot handle logs or tracing data (requires <strong>Loki</strong> for logs, <strong>Jaeger</strong> for traces).</li>
  <li><strong>Not Long-Term Storage:</strong> Requires <strong>Remote Storage</strong> (like Thanos) for long-term archiving.</li>
</ul>

<h3>Major Configuration for Setup (via Helm Values)</h3>
<table>
  <thead>
    <tr>
      <th>Configuration Item</th>
      <th>File/Location</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>scrape_configs</strong></td>
      <td><code>prometheus.yml</code> (in ConfigMap)</td>
      <td>Defines how and where to find targets using Kubernetes Service Discovery.</td>
    </tr>
    <tr>
      <td><strong>relabel_configs</strong></td>
      <td><code>scrape_configs</code></td>
      <td>Crucial for modifying labels to set the correct metrics path and target port.</td>
    </tr>
    <tr>
      <td><strong>job_name</strong></td>
      <td><code>scrape_configs</code></td>
      <td>A mandatory label used to group all metrics scraped from a single configuration.</td>
    </tr>
  </tbody>
</table>

<h3>Basic PromQL Commands 💡</h3>
<table>
  <thead>
    <tr>
      <th>Command</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>up</code></td>
      <td>Shows the health of a scraped target (1 for up, 0 for down).</td>
    </tr>
    <tr>
      <td><code>rate(http_requests_total[5m])</code></td>
      <td>Calculates the average per-second rate of HTTP requests over the last 5 minutes.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>📊 III. Grafana: Concepts, Advantages, &amp; Setup</h2>
<p>Grafana is the <strong>visualization and analytics layer</strong>.</p>

<h3>Basic Concepts</h3>
<ul>
  <li><strong>Data Source:</strong> A stored connection (like the Prometheus ClusterIP) used to fetch data.</li>
  <li><strong>Dashboard:</strong> A container for multiple <strong>Panels</strong> organized into rows.</li>
  <li><strong>Panel:</strong> A single visualization unit (Graph, Singlestat, Gauge) running a specific PromQL query.</li>
  <li><strong>Variables (Templating):</strong> Dynamic placeholders (like <code>$instance</code>) used in queries to make dashboards reusable.</li>
  <li><strong>Provisioning:</strong> Automates creation of Data Sources and Dashboards on startup (via Helm configuration).</li>
</ul>

<h3>Advantages (Pros) ✅</h3>
<ul>
  <li><strong>Versatility:</strong> Connects to many data sources (Prometheus, Loki, MySQL).</li>
  <li><strong>Templating:</strong> Enables highly dynamic, reusable dashboards with dynamic dropdowns.</li>
  <li><strong>Provisioning:</strong> Setup is repeatable and version-controlled via Helm/Git.</li>
</ul>

<h3>Limitations (Cons) 🚫</h3>
<ul>
  <li><strong>No Data Storage:</strong> Purely a query tool; requires a functioning data source (Prometheus) to display anything.</li>
  <li><strong>Configuration Overhead:</strong> Designing complex dashboards requires JSON/templating knowledge.</li>
</ul>

<h3>Major Configuration for Setup</h3>
<table>
  <thead>
    <tr>
      <th>Configuration Item</th>
      <th>File/Location</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Data Source YAML</strong></td>
      <td>Helm Chart Provisioning (<code>datasources.yaml</code>)</td>
      <td>Defines the connection details to your Prometheus service.</td>
    </tr>
    <tr>
      <td><strong>Dashboard JSON</strong></td>
      <td>Helm Chart Provisioning (<code>dashboards.yaml</code>)</td>
      <td>Defines the visualization layout, panels, and queries loaded on startup.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>🎯 IV. What the Stack Achieves</h2>
<p>By implementing this stack, you achieve core <strong>Operational Observability</strong> for your microservices:</p>

<ol>
  <li><strong>System Health:</strong> Real-time uptime monitoring (<code>up</code> metric).</li>
  <li><strong>Performance:</strong> Track API request rates, error rates, and response times.</li>
  <li><strong>Capacity Planning:</strong> Monitor CPU, Memory, and JVM heap usage to inform scaling decisions.</li>
  <li><strong>Debugging/Triage:</strong> Correlate user-facing issues with specific component failures shown on dashboards.</li>
</ol>

<hr>
<hr/>
<!-- 🚀 Prometheus Setup: Helm Deployment Notes -->

<h1>🚀 Prometheus Setup: Helm Deployment Notes</h1>
<p>We use the standard <strong><code>kube-prometheus-stack</code></strong> Helm chart, which includes Prometheus Server, Alertmanager, and Grafana, adhering to industry best practices.</p>

<hr>

<h2>📋 Prerequisites (Before Deployment)</h2>

<ol>
  <li><strong>Helm CLI:</strong> Must be installed and configured.</li>
  <li><strong>Helm Repo:</strong> The community repository must be added:
    <pre><code class="language-bash">
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
    </code></pre>
  </li>
  <li><strong>Ports Reserved:</strong> Your <code>cluster.yaml</code> reserves <strong>NodePort 30104</strong> for Prometheus, which we will use for external UI access.</li>
</ol>

<hr>

<h2>⚙️ Step 1: Define Custom Configuration (<code>prometheus-values.yaml</code>)</h2>

<p>Create a custom file to configure persistence for the Kind environment and expose the UI via NodePort.</p>

<pre><code class="language-yaml">
# prometheus-values.yaml

# 1. Disable Persistence (CRITICAL for Kind/WSL testing)
# We use emptyDir for testing since dynamic provisioning is complex in Kind.
prometheus:
  prometheusSpec:
    storageSpec:
      volumeClaimTemplate:
        spec:
          storageClassName: "" # Use default (or empty string to signal non-PVC)
          resources:
            requests:
              storage: 10Gi

# 2. Expose Prometheus UI via NodePort
# This uses your reserved port 30104
prometheus:
  service:
    type: NodePort
    nodePort: 30104
    targetPort: 9090 # Default Prometheus port
    port: 9090

# 3. Disable Grafana/Alertmanager NodePorts (Optional cleanup, for security/organization)
# We usually expose only the main tool (Prometheus) first.
alertmanager:
  ingress:
    enabled: false
grafana:
  ingress:
    enabled: false
  service:
    type: ClusterIP # Keep Grafana internal for now
</code></pre>

<hr>

<h2>📦 Step 2: Install the Chart</h2>

<p>Execute the installation using the release name <code>prom-stack</code> and the customized values file. The <code>--create-namespace</code> flag handles namespace creation.</p>

<pre><code class="language-bash">
helm install prom-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --version 58.0.0 \
  -f prometheus-values.yaml
</code></pre>

<hr>

<h2>🔍 Step 3: Final Status Check (Verification)</h2>

<p>After waiting a few minutes for the large components to pull and initialize, check the status of all pods in the <code>monitoring</code> namespace.</p>

<pre><code class="language-bash">
kubectl get pods -n monitoring
</code></pre>

<p><strong>Expected Healthy Output:</strong> All key components must be in a <strong><code>Running</code></strong> state, with the <code>READY</code> column showing the required number of containers.</p>

<table>
  <thead>
    <tr>
      <th>Component Name</th>
      <th>Description</th>
      <th>Expected Status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>prometheus-prom-stack-kube-prometheus-prometheus-0</code></td>
      <td><strong>Prometheus Server</strong> (The Collector)</td>
      <td><code>Running</code></td>
    </tr>
    <tr>
      <td><code>alertmanager-prom-stack-kube-prometheus-alertmanager-0</code></td>
      <td><strong>Alertmanager</strong> (The Alerter)</td>
      <td><code>Running</code></td>
    </tr>
    <tr>
      <td><code>prom-stack-grafana-xxxxx</code></td>
      <td><strong>Grafana</strong> (The Visualizer)</td>
      <td><code>Running</code></td>
    </tr>
    <tr>
      <td><code>prom-stack-kube-prometheus-operator-xxxxx</code></td>
      <td><strong>Operator</strong> (Manages CRDs)</td>
      <td><code>Running</code></td>
    </tr>
    <tr>
      <td><code>prom-stack-prometheus-node-exporter-xxxxx</code></td>
      <td><strong>Node Exporter</strong> (Host Metrics)</td>
      <td><code>Running</code></td>
    </tr>
  </tbody>
</table>

<p><strong>Your successful output confirms this step is complete:</strong></p>

<pre><code class="language-bash">
alertmanager-prom-stack-kube-prometheus-alertmanager-0    2/2     Running   0         2m41s
prom-stack-grafana-7cbb8676fb-xcz56                      3/3     Running   0         3m16s
prometheus-prom-stack-kube-prometheus-prometheus-0       2/2     Running   0         2m40s
# ... (Other supporting pods)
</code></pre>

<hr>
<p style="text-align:center; font-style:italic;">✅ Prometheus Stack successfully deployed using Helm!</p>
<hr/>
<hr/>

<hr/>
<h1>🚀 V. Finalizing Prometheus: Scraping the Sender Service (Full Log)</h1>

<p>This section documents the specific configurations and commands executed to resolve the deployment and connection issues, resulting in the successful monitoring of the <strong>Sender Service</strong>.</p>

<hr>

<h2>1. Initial State &amp; Problem Diagnosis</h2>

<ul>
  <li><strong>Initial State:</strong> The Prometheus server was running, and the <code>all-microservices-monitor</code> was deployed.</li>
  <li><strong>Error Received:</strong> <code>connection refused</code> (Prometheus could not connect to the Pod IP <code>10.244.x.x:8098</code>).</li>
  <li><strong>Diagnosis:</strong> The Spring Boot application was not binding to the correct network interface (<code>0.0.0.0</code>) inside the container.</li>
</ul>

<hr>

<h2>2. Fix 1: Service Port Naming and Labeling</h2>

<p>The <code>ServiceMonitor</code> requires the Kubernetes Service port to be explicitly named (<code>http</code>). This was solved by updating the Helm chart structure.</p>

<table>
  <thead>
    <tr><th>File</th><th>Change Required</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><code>senderservice/service.yaml</code> (Template)</td>
      <td>Added <code>name: http</code> to the ports list.</td>
    </tr>
  </tbody>
</table>

<p><strong>Command Executed:</strong></p>

<pre><code>helm upgrade senderservice ./senderservice-chart -n sender
</code></pre>

<p><code>helpers.tpl</code> — Fixed missing label definitions.</p>

<pre><code>helm upgrade senderservice ./senderservice-chart -n sender
</code></pre>

<p><strong>Verification Command:</strong></p>

<pre><code class="language-bash"># Verify the port is now named 'http'
kubectl get svc -n sender senderservice-service -o jsonpath='{.spec.ports[0].name}'
</code></pre>

<p><strong>Expected Output:</strong> <code>http</code></p>

<hr>

<h2>3. Fix 2: Application Binding (<code>0.0.0.0</code> Fix)</h2>

<p>The network issue was resolved by explicitly forcing the Spring Boot application to listen on all interfaces using the <code>SERVER_ADDRESS</code> environment variable.</p>

<table>
  <thead>
    <tr><th>File</th><th>Code/Config Change</th><th>Purpose</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><code>senderservice/values.yaml</code></td>
      <td>Added <code>SERVER_ADDRESS: "0.0.0.0"</code> to the <code>env</code> block.</td>
      <td>Forces the embedded server (Tomcat) to bind to all IPs.</td>
    </tr>
    <tr>
      <td><code>senderservice/deployment.yaml</code></td>
      <td>Mapped this new value to the container.</td>
      <td>Ensures the <code>SERVER_ADDRESS</code> is passed to the Spring Boot application.</td>
    </tr>
  </tbody>
</table>

<p><strong>Kubernetes Commands:</strong></p>

<pre><code class="language-bash"># 1. Check variable inside running Pod
kubectl exec -n sender &lt;SENDER_POD_NAME&gt; -- env | grep SERVER_ADDRESS

# 2. Output Confirmed
SERVER_ADDRESS=0.0.0.0
</code></pre>

<hr>

<h2>4. Final Status Check and Monitoring Confirmation</h2>

<p>The combined fixes allowed the connection to succeed, and the target became visible and healthy.</p>

<table>
  <thead>
    <tr><th>Action</th><th>Command Executed</th><th>Status Confirmed</th></tr>
  </thead>
  <tbody>
    <tr>
      <td>Monitor Deployment</td>
      <td><code>kubectl apply -f microservice-monitor.yaml -n monitoring</code></td>
      <td><code>all-microservices-monitor</code> created.</td>
    </tr>
    <tr>
      <td>Prometheus Restart</td>
      <td><code>kubectl rollout restart deployment receiverservice-deployment -n receiver</code></td>
      <td>New Pod created with correct configuration.</td>
    </tr>
  </tbody>
</table>

<p><strong>UI Verification:</strong></p>
<p>Access <a href="http://localhost:30104/targets" target="_blank">http://localhost:30104/targets</a></p>

<p><strong>Sender Service shows UP (Green).</strong></p>

<pre><code>Final Targets Page Output:
Endpoint: http://10.244.2.8:8098/actuator/prometheus
State: UP
Labels: job="senderservice-senderservice-service", endpoint="http"
</code></pre>

<hr>

<h2>5. Summary of Required Dependencies &amp; Configuration</h2>

<p>For the Sender Service to expose metrics correctly, the following items must be present in the build and configuration:</p>

<table>
  <thead>
    <tr><th>File</th><th>Configuration / Dependency</th><th>Purpose</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><code>senderservice/pom.xml</code></td>
      <td><code>spring-boot-starter-actuator</code></td>
      <td>Exposes the base <code>/actuator</code> endpoints.</td>
    </tr>
    <tr>
      <td><code>senderservice/pom.xml</code></td>
      <td><code>micrometer-registry-prometheus</code></td>
      <td>Formats all JVM/Spring metrics into Prometheus format.</td>
    </tr>
    <tr>
      <td><code>application.properties</code></td>
      <td><code>management.endpoints.web.exposure.include=prometheus</code></td>
      <td>Enables the <code>/actuator/prometheus</code> endpoint.</td>
    </tr>
    <tr>
      <td><code>K8s Env Var</code></td>
      <td><code>SERVER_ADDRESS=0.0.0.0</code></td>
      <td><strong>Crucial:</strong> Allows external Pods (Prometheus) to connect to the application.</td>
    </tr>
  </tbody>
</table>

<p><strong>✅ The Sender Service is now fully observable via Prometheus!</strong></p>

<hr>

<footer>
  <p><em>Documentation maintained for Helm + Prometheus monitoring setup validation.</em></p>
</footer>
<hr/>
<hr/>

<hr/>
<hr/>
<h1>📊 PromQL Queries for Sender Service: RED &amp; USE Monitoring</h1>

<p>You're focusing on the essential areas of observability for your microservices: <strong>RED (Rate, Errors, Duration)</strong> and <strong>USE (Utilization, Saturation, Errors)</strong>.</p>

<p>This guide covers the core concepts of writing PromQL queries and the specific queries for your stack (assuming your service is the <strong>Sender Service</strong> with label <code>job="senderservice"</code>).</p>

<hr>

<h2>📝 PromQL Basics: How to Write a Query</h2>

<p>Every PromQL query is built from two components: <strong>Selector</strong> (what data to get) and <strong>Functions</strong> (how to calculate it).</p>

<h3>1. The Core Selector</h3>

<p>Queries start by selecting a <strong>Metric Name</strong> and using <strong>Label Selectors</strong> (filters in curly braces <code>{}</code>) to narrow down the source.</p>

<table>
  <thead>
    <tr>
      <th>Element</th>
      <th>Example</th>
      <th>Meaning</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Metric Name</strong></td>
      <td><code>http_server_requests_seconds_count</code></td>
      <td>The base counter for HTTP requests.</td>
    </tr>
    <tr>
      <td><strong>Label Selector</strong></td>
      <td><code>{job="senderservice"}</code></td>
      <td>Filters the data to include only the Sender Service.</td>
    </tr>
    <tr>
      <td><strong>Range Vector</strong></td>
      <td><code>[5m]</code></td>
      <td>Specifies the time window (5 minutes) for functions to calculate over.</td>
    </tr>
  </tbody>
</table>

<h3>2. The Core Function: <code>rate()</code></h3>

<p>Your application uses <strong>Counters</strong> (metrics that only go up). To calculate the speed of change (like Requests Per Second), you must use the <code>rate()</code> function.</p>

<table>
  <thead>
    <tr>
      <th>Query Type</th>
      <th>Formula</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Rate (RPS/EPS)</strong></td>
      <td><code>rate(counter_metric[time_range])</code></td>
      <td>Calculates the per-second average increase over the time range.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>🚦 Application Metrics (RED Monitoring)</h2>

<p>These queries use the Spring Boot metric <code>http_server_requests_seconds_count</code> for rate and error checks, and the corresponding <code>_sum</code> for latency.</p>

<h3>1. Traffic Rate (RPS) and Throughput</h3>

<p>This measures how busy your service is. <strong>Rate</strong> gives you the total load, and <strong>Throughput</strong> breaks that load down by component (endpoint).</p>

<table>
  <thead>
    <tr>
      <th>Metric</th>
      <th>PromQL Query</th>
      <th>Calculation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Total Traffic Rate (RPS)</strong></td>
      <td><code>sum(rate(http_server_requests_seconds_count{job="senderservice"}[5m]))</code></td>
      <td>Sums the request rate from all Pods of the service.</td>
    </tr>
    <tr>
      <td><strong>Throughput by Endpoint</strong></td>
      <td><code>sum(rate(http_server_requests_seconds_count{job="senderservice"}[5m])) by (uri)</code></td>
      <td>Shows the RPS handled by each individual endpoint (<code>/send</code>, <code>/search</code>, etc.).</td>
    </tr>
  </tbody>
</table>

<h3>2. Error Rate (EPS) and Percentage</h3>

<p>This measures the quality of your service responses. We filter the traffic metric using the <code>outcome</code> label.</p>

<table>
  <thead>
    <tr>
      <th>Metric</th>
      <th>PromQL Query</th>
      <th>Calculation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Total Error Rate (EPS)</strong></td>
      <td><code>sum(rate(http_server_requests_seconds_count{job="senderservice", outcome=~"CLIENT_ERROR|SERVER_ERROR"}[5m]))</code></td>
      <td>Sums the per-second rate of all requests resulting in 4xx or 5xx status codes.</td>
    </tr>
    <tr>
      <td><strong>Error Percentage</strong></td>
      <td><code>( total_error_rate / total_traffic_rate ) * 100</code></td>
      <td>Divide the Error Rate by total RPS and multiply by 100 (best calculated in Grafana).</td>
    </tr>
  </tbody>
</table>

<h3>3. Latency (Duration)</h3>

<p>This measures the speed of your service responses. It requires calculating the average from the dedicated latency metrics (Histograms/Summaries).</p>

<table>
  <thead>
    <tr>
      <th>Metric</th>
      <th>PromQL Query</th>
      <th>Calculation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Average Latency</strong></td>
      <td><code>sum(rate(http_server_requests_seconds_sum{job="senderservice"}[5m])) / sum(rate(http_server_requests_seconds_count{job="senderservice"}[5m]))</code></td>
      <td>Divides total time spent by total requests over 5 minutes to get mean latency (in seconds).</td>
    </tr>
    <tr>
      <td><strong>p99 Latency (99th percentile)</strong></td>
      <td><code>histogram_quantile(0.99, sum(rate(http_server_requests_seconds_bucket{job="senderservice"}[5m])) by (le))</code></td>
      <td>Latency below which 99% of requests fall. A key metric for service quality.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>💻 System Metrics (USE Monitoring)</h2>

<p>These metrics are typically scraped by the <strong>Node Exporter</strong> (for CPU/Memory/Storage of the host machine) and <strong>JVM metrics</strong> (for the application's process).</p>

<h3>1. CPU Utilization</h3>

<table>
  <thead>
    <tr>
      <th>Metric</th>
      <th>PromQL Query</th>
      <th>Calculation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Application CPU Usage</strong></td>
      <td><code>sum(rate(process_cpu_usage_seconds_total{job="senderservice"}[5m]))</code></td>
      <td>Measures total CPU seconds consumed by the Java process over 5 minutes.</td>
    </tr>
    <tr>
      <td><strong>Node CPU Utilization</strong></td>
      <td><code>100 - avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) by (instance) * 100</code></td>
      <td>Shows the percentage of CPU actively being used on the host machine.</td>
    </tr>
  </tbody>
</table>

<h3>2. Memory Usage</h3>

<table>
  <thead>
    <tr>
      <th>Metric</th>
      <th>PromQL Query</th>
      <th>Calculation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Application Memory (JVM Heap)</strong></td>
      <td><code>jvm_memory_used_bytes{job="senderservice", area="heap"}</code></td>
      <td>Shows current memory usage of the Java heap (Gauge).</td>
    </tr>
    <tr>
      <td><strong>Node Memory Utilization</strong></td>
      <td><code>(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100</code></td>
      <td>Percentage of total physical memory currently in use on the host machine.</td>
    </tr>
  </tbody>
</table>

<h3>3. Storage (Disk I/O and Space)</h3>

<table>
  <thead>
    <tr>
      <th>Metric</th>
      <th>PromQL Query</th>
      <th>Calculation</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Disk Space Available</strong></td>
      <td><code>node_filesystem_avail_bytes{mountpoint="/"} / (1024 * 1024 * 1024)</code></td>
      <td>Shows free space on the host machine's root partition (in GB).</td>
    </tr>
    <tr>
      <td><strong>Disk Write Rate</strong></td>
      <td><code>sum(rate(node_disk_written_bytes_total[5m]))</code></td>
      <td>Calculates total write throughput (bytes per second) across the node's disks.</td>
    </tr>
  </tbody>
</table>

<hr>

<footer>
  <p><em>✅ These PromQL queries form the foundation for effective RED and USE monitoring dashboards in Grafana for the Sender Service.</em></p>
</footer>
<hr/>
<hr/>
<h2>Prometheus cAdvisor Metrics Issue Explained</h2>

<p>
The fact that your query for the CPU metric returns <code>{}</code> indicates that Prometheus is 
<b>not collecting metrics from the Kubelet endpoints</b> (cAdvisor metrics) for the containers in the 
<code>sender</code> namespace.
</p>

<p>
The output confirming the <b>ServiceMonitor is up</b> 
(<code>ServiceMonitor/monitoring/prom-stack-kube-state-metrics/0 (1/1 up)</code>) is misleading. 
This specific ServiceMonitor only targets the <b>kube-state-metrics (KSM)</b> deployment, which gives you 
<b>status data</b> (Running, Pending, Restarts) but <b>NOT resource usage data</b> (CPU, Memory, Bandwidth).
</p>

<hr/>

<h3><b>The Problem and the Solution</b></h3>

<table>
  <thead>
    <tr>
      <th>Component</th>
      <th>Responsibility</th>
      <th>Status</th>
      <th>Implication</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>Kube-State-Metrics (KSM)</b></td>
      <td><b>Status/Health</b> (Pod Phases, Restarts)</td>
      <td>✅ <b>UP</b> (Scraped successfully)</td>
      <td>This is why your <code>kube_pod_status_phase</code> query worked.</td>
    </tr>
    <tr>
      <td><b>Kubelet / cAdvisor</b></td>
      <td><b>Resource Usage</b> (CPU/Memory utilization)</td>
      <td>❌ <b>NOT Scraped</b></td>
      <td>
        This is why your <code>container_cpu_usage_seconds_total</code> query failed. 
        This metric comes directly from the Kubelet agent on the node.
      </td>
    </tr>
  </tbody>
</table>

<p>
Your Prometheus configuration is missing the scrape job or ServiceMonitor that targets the 
<b>Kubelet's cAdvisor endpoint</b> on your worker nodes.
</p>

<hr/>

<h2>📝 Essential PromQL Queries for Kubernetes Cluster Monitoring</h2>

<p>
To get comprehensive monitoring for health and resource usage, you need to use a combination 
of metrics from <b>kube-state-metrics (KSM)</b> and <b>cAdvisor (Container Metrics)</b>.
</p>

<h3>I. Pod Health &amp; Availability (KSM Metrics)</h3>

<p>
These metrics monitor the desired state of your application across all containers and pods.
</p>

<table>
  <thead>
    <tr>
      <th>Goal</th>
      <th>PromQL Query</th>
      <th>Notes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>Total Pod Count (by Namespace)</b></td>
      <td><code>sum by (namespace) (kube_pod_info)</code></td>
      <td>Counts all pods (Running, Pending, Succeeded, etc.).</td>
    </tr>
    <tr>
      <td><b>Pods Ready to Serve Traffic</b></td>
      <td><code>sum by (namespace) (kube_pod_status_ready{condition="true"})</code></td>
      <td><b>Best health indicator.</b> Pods that have passed all readiness checks.</td>
    </tr>
    <tr>
      <td><b>Pod Instability (Restarts)</b></td>
      <td><code>sum by (namespace) (increase(kube_pod_container_status_restarts_total[5m]))</code></td>
      <td>Calculates the total number of container restarts across namespaces in the last 5 minutes.</td>
    </tr>
    <tr>
      <td><b>Pods NOT Ready / Stuck</b></td>
      <td><code>count by (namespace) (kube_pod_status_ready{condition="false"})</code></td>
      <td>Identifies pods that are currently unhealthy or stuck in a non-ready state.</td>
    </tr>
  </tbody>
</table>

<hr/>

<h3>II. Pod Resource Utilization (cAdvisor Metrics)</h3>

<p>
These metrics measure the actual consumption of your containers — essential for right-sizing and 
performance troubleshooting.
</p>

<table>
  <thead>
    <tr>
      <th>Goal</th>
      <th>PromQL Query</th>
      <th>Notes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><b>Actual CPU Usage (Cores)</b></td>
      <td><code>sum(rate(container_cpu_usage_seconds_total{container!="", namespace="&lt;NAMESPACE&gt;"}[5m])) by (pod)</code></td>
      <td>Shows the average number of <b>CPU cores</b> currently being consumed by each pod.</td>
    </tr>
    <tr>
      <td><b>Actual Memory Used (Bytes)</b></td>
      <td><code>sum(container_memory_working_set_bytes{container!="", namespace="&lt;NAMESPACE&gt;"}) by (pod)</code></td>
      <td>Shows the <b>working set size</b> of memory (in bytes) used by each pod. Divide by <code>1024*1024*1024</code> to get GiB.</td>
    </tr>
    <tr>
      <td><b>CPU Utilization % (vs. Limit)</b></td>
      <td><code>(sum(rate(container_cpu_usage_seconds_total{container!="", namespace="&lt;NAMESPACE&gt;"}[5m])) by (pod) / sum(kube_pod_container_resource_limits{resource="cpu", namespace="&lt;NAMESPACE&gt;"}) by (pod)) * 100</code></td>
      <td>Shows if a pod is approaching its configured CPU limit (potential for throttling).</td>
    </tr>
  </tbody>
</table>

<hr/>

<h2>🛠️ The Fix: Scrape the Kubelet</h2>

<p>
You need to check your Prometheus configuration (<code>prometheus.yml</code> or the associated 
ServiceMonitor/PodMonitor) to ensure you have a job that explicitly scrapes the 
<b>Kubelet endpoints</b> on every node.
</p>

<p>
You must add a job targeting metrics at the <code>container_*</code> level. 
In standard setups, this job usually looks like this:
</p>

<pre><code class="language-yaml">
- job_name: 'kubernetes-cadvisor'
  kubernetes_sd_configs:
  - role: node
  # ... other configuration for service discovery and TLS skip ...
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: (.+):10250
    target_label: __address__
    replacement: $1:10250 # Ensure we are hitting the cAdvisor metrics port
</code></pre>

<p>
✅ Once added, Prometheus will begin scraping container-level metrics from cAdvisor, 
giving you visibility into CPU, memory, and disk usage across all pods.
</p>
<hr/>
<hr/>
<h2>📊 Grafana: Concepts, Setup, and Data Source Integration</h2>

<p>
Grafana is the <strong>visualization, analytics, and alerting platform</strong> that serves as the frontend for your monitoring stack. 
It takes the time-series data collected by Prometheus and transforms it into intuitive, interactive dashboards.
</p>

<hr />

<h3>1. What is Grafana and Its Use?</h3>

<table>
  <thead>
    <tr>
      <th>Concept</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Role</strong></td>
      <td><strong>Visualization Layer.</strong> Grafana does not collect or store metrics; it acts solely as a querying and presentation engine.</td>
    </tr>
    <tr>
      <td><strong>Core Use</strong></td>
      <td><strong>Data Interpretation.</strong> It helps DevOps teams, engineers, and stakeholders monitor system health, track application performance, and identify anomalies by viewing metrics over time.</td>
    </tr>
    <tr>
      <td><strong>Data Source Agnostic</strong></td>
      <td>Grafana is versatile and connects to dozens of different data backends (<strong>Data Sources</strong>), including Prometheus, Loki (for logs), MySQL, MongoDB, and Elasticsearch.</td>
    </tr>
  </tbody>
</table>

<hr />

<h3>2. Basic Concepts</h3>

<ul>
  <li><strong>Data Source:</strong> A stored connection detail (like the URL and credentials) that Grafana uses to query metrics from a backend like Prometheus.</li>
  <li><strong>Dashboard:</strong> A collection of panels organized into rows. This is where you bring related metrics together (e.g., a "Kafka Metrics Dashboard").</li>
  <li><strong>Panel:</strong> A single visualization element (e.g., a Graph, Gauge, or Singlestat) that runs a specific PromQL query against a data source.</li>
  <li><strong>Variables (Templating):</strong> Dynamic placeholders (e.g., <code>$namespace</code>, <code>$instance</code>) that allow one dashboard to be reusable across many services or environments.</li>
</ul>

<hr />

<h3>3. How to Set Up Grafana (Leveraging Helm)</h3>

<p>
Since you deployed the <strong><code>kube-prometheus-stack</code></strong>, Grafana is already installed in your <code>monitoring</code> namespace, dramatically simplifying the process.
</p>

<table>
  <thead>
    <tr>
      <th>Step</th>
      <th>Action</th>
      <th>Command/Details</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>A. Retrieve Password</strong></td>
      <td>Get the random admin password from the Kubernetes Secret.</td>
      <td><code>kubectl get secret prom-stack-grafana -n monitoring -o jsonpath='{.data.admin-password}' | base64 -d</code></td>
    </tr>
    <tr>
      <td><strong>B. Access UI (NodePort)</strong></td>
      <td>Access Grafana using the reserved NodePort (which maps to internal port 3000).</td>
      <td><strong>URL:</strong> <code>http://localhost:30107</code></td>
    </tr>
    <tr>
      <td><strong>C. Login</strong></td>
      <td>Log in with the default credentials.</td>
      <td><strong>Username:</strong> <code>admin</code> / <strong>Password:</strong> (Retrieved in Step A)</td>
    </tr>
  </tbody>
</table>

<hr />

<h3>4. How to Add Prometheus as a Data Source (Manual Method)</h3>

<p>
When installed via the <code>kube-prometheus-stack</code>, Prometheus is usually <strong>auto-provisioned</strong> as a data source. However, if the automatic connection fails, you must do it manually.
</p>

<table>
  <thead>
    <tr>
      <th>Field in Grafana UI</th>
      <th>Value to Enter</th>
      <th>Purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Name</strong></td>
      <td><code>Prometheus-Cluster</code></td>
      <td>A descriptive name for the data source.</td>
    </tr>
    <tr>
      <td><strong>Type</strong></td>
      <td><code>Prometheus</code></td>
      <td>Select the correct data backend type.</td>
    </tr>
    <tr>
      <td><strong>HTTP URL (Crucial Fix)</strong></td>
      <td><code>http://prom-stack-kube-prometheus-prometheus.monitoring.svc.cluster.local:9090</code></td>
      <td><strong>MUST</strong> use the internal Kubernetes ClusterIP DNS name so Grafana (internal pod) can reach Prometheus (internal service). 
      (Using <code>localhost:30104</code> causes the connection refused error).</td>
    </tr>
    <tr>
      <td><strong>Access</strong></td>
      <td><code>Server</code> (Default)</td>
      <td>Tells Grafana to query Prometheus directly from the backend server.</td>
    </tr>
    <tr>
      <td><strong>Save & Test</strong></td>
      <td>Click the button.</td>
      <td>Verifies the connection and confirms “Data source is working.”</td>
    </tr>
  </tbody>
</table>

<p>
Once the data source is connected, you can start running your PromQL queries and building visualization panels for your Sender Service’s traffic and health.
</p>
<hr/>
<hr/>
<hr/>
<h1>📊 Grafana Control Plane Overview Dashboard (with Namespace Templating)</h1>

<hr/>

<h2>🎯 Objective</h2>
<p>
Create a reusable <strong>Control Plane Overview Dashboard</strong> in Grafana using 
templated variables and stat panels. This dashboard dynamically displays <strong>Running</strong> 
and <strong>Non-Running</strong> Kubernetes Pods per namespace.
</p>

<hr/>

<h2>🛠️ Step 1: Create the Namespace Variable</h2>

<p>
We define a reusable variable (<code>$namespace</code>) that automatically populates a dropdown 
list with all available Kubernetes namespaces from Prometheus.
</p>

<ol>
  <li><strong>Open Dashboard Settings:</strong> Click the <strong>Gear icon</strong> ⚙️ in the top menu.</li>
  <li>Go to <strong>Variables → New Variable</strong> → <strong>Add Variable</strong>.</li>
</ol>

<table>
  <thead>
    <tr><th>Field</th><th>Setting</th><th>Explanation</th></tr>
  </thead>
  <tbody>
    <tr><td><strong>Name</strong></td><td><code>namespace</code></td><td>The variable name used in PromQL queries.</td></tr>
    <tr><td><strong>Type</strong></td><td><code>Query</code></td><td>We will query Prometheus for namespace labels.</td></tr>
    <tr><td><strong>Data Source</strong></td><td><code>Prometheus</code></td><td>Select your configured Prometheus data source.</td></tr>
    <tr><td><strong>Query (PromQL)</strong></td><td><code>label_values(kube_namespace_labels, namespace)</code></td><td>Fetches all available namespaces from Kube-State-Metrics.</td></tr>
    <tr><td><strong>Regex</strong></td><td><code>/.*$/</code></td><td>Keep as default. Optionally use <code>/^$|^.+$/</code> if filtering issues occur.</td></tr>
    <tr><td><strong>Selection Options</strong></td><td><strong>Enable "Include All option"</strong></td><td>This adds the <strong>All</strong> selection (represented as <code>.*</code> in PromQL).</td></tr>
  </tbody>
</table>

<p>
Click <strong>Update</strong>. You should now see a dropdown at the top of your dashboard 
listing all namespa
<hr/>
<hr/>
<hr/>

<h1 align="center">🚨 Grafana Alerting: Production-Ready Setup Guide</h1>

<p><strong>Setting up alerting in Grafana</strong> is a critical step for production readiness. It ensures that any issues detected by <code>Prometheus</code> are routed to the right people using <code>Grafana</code> and <code>Alertmanager</code>.</p>

<hr/>

<h2>🔍 Overview</h2>

<p>In the modern Prometheus stack (Grafana 9+ or the <code>kube-prometheus-stack</code>), Grafana serves as the central hub for creating, managing, and viewing alerts. However, it still depends on <strong>Alertmanager</strong> for routing notifications to appropriate channels.</p>

<hr/>

<h2>🧩 Key Components</h2>

<table>
  <tr>
    <th>Component</th>
    <th>Role</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><strong>Alert Rule</strong></td>
    <td><strong>Detection</strong></td>
    <td>Defined in Grafana (or Prometheus). It contains the <code>PromQL</code> query and threshold to move the alert through states <em>Normal → Pending → Firing</em>.</td>
  </tr>
  <tr>
    <td><strong>Alertmanager</strong></td>
    <td><strong>Routing & Grouping</strong></td>
    <td>Handles alert grouping, deduplication, and routing to contact points to prevent alert storms.</td>
  </tr>
  <tr>
    <td><strong>Contact Point</strong></td>
    <td><strong>Receiver</strong></td>
    <td>Defines external endpoints like Email, Teams, or Slack where alerts are delivered.</td>
  </tr>
  <tr>
    <td><strong>Notification Policy</strong></td>
    <td><strong>Routing Logic</strong></td>
    <td>Specifies which alerts (based on labels like <code>severity="critical"</code>) go to which contact points.</td>
  </tr>
</table>

<hr/>

<h2>⚙️ Alert States</h2>

<ol>
  <li><strong>Inactive (Normal):</strong> Condition not met.</li>
  <li><strong>Pending:</strong> Condition met briefly; waiting to confirm (prevents flapping).</li>
  <li><strong>Firing:</strong> Condition persists beyond the configured duration (<code>for:</code>).</li>
</ol>

<hr/>

<h2>🛠️ Implementation Plan: Detect Pod Down</h2>

<p>We'll create an alert rule using the <code>up</code> metric to detect if a Kubernetes Pod is down and send alerts via Email and Teams.</p>

<h3>Step 1: Configure Contact Points (Email & Teams)</h3>

<ol>
  <li>Access Grafana: <code>http://localhost:30107</code></li>
  <li>Go to <strong>Alerting → Contact points</strong>.</li>
  <li><strong>Email Contact:</strong>
    <ul>
      <li><strong>Name:</strong> Admin-Email-Contact</li>
      <li><strong>Type:</strong> Email</li>
      <li><strong>Addresses:</strong> Enter admin email(s)</li>
    </ul>
  </li>
  <li><strong>Microsoft Teams Contact:</strong>
    <ul>
      <li><strong>Name:</strong> Teams-DevOps-Channel</li>
      <li><strong>Type:</strong> Microsoft Teams</li>
      <li><strong>Webhook URL:</strong> Paste your Teams channel webhook</li>
    </ul>
  </li>
</ol>

<h3>Step 2: Create the Alert Rule</h3>

<p>This alert triggers if the <code>Sender Service Pod</code> is down (<code>up == 0</code>) for more than one minute.</p>

<table>
  <tr><th>Field</th><th>Setting</th><th>Explanation</th></tr>
  <tr><td><strong>Rule Name</strong></td><td>SenderServicePodDown</td><td>Name describing the issue</td></tr>
  <tr><td><strong>Query (A)</strong></td><td><code>up{job="senderservice-senderservice-service"}</code></td><td>Fetches Pod health metric</td></tr>
  <tr><td><strong>Condition</strong></td><td><code>A is below 1</code></td><td>Triggers when health value is 0 (DOWN)</td></tr>
  <tr><td><strong>For</strong></td><td>1m</td><td>Must persist for 1 minute before firing</td></tr>
  <tr><td><strong>Severity Label</strong></td><td><code>severity="critical"</code></td><td>Used for routing</td></tr>
</table>

<h3>Step 3: Notification Policy</h3>

<ol>
  <li>Go to <strong>Alerting → Notification policies</strong>.</li>
  <li>Create a new policy:</li>
</ol>

<ul>
  <li><strong>Match Label:</strong> <code>severity="critical"</code></li>
  <li><strong>Contact Points:</strong> Admin-Email-Contact, Teams-DevOps-Channel</li>
</ul>

<p>Now, any alert with <code>severity=critical</code> will notify both Email and Microsoft Teams simultaneously.</p>

<hr/>

<h2>✅ Summary</h2>
<ul>
  <li>Grafana handles alerting visualization and management.</li>
  <li>Prometheus collects metrics; Alertmanager handles routing.</li>
  <li>Contact points define alert destinations.</li>
  <li>Notification policies define the routing logic.</li>
  <li>This setup ensures automated and consistent alerting for critical production issues.</li>
</ul>
