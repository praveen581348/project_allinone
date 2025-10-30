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

