<h1>📘 SDLC & DevOps Lifecycle – Full Toolchain & Architecture Mapping</h1>

<p>This document describes how traditional Software Development Life Cycle (SDLC) integrates seamlessly with DevOps to deliver scalable, secure, and resilient cloud-native applications.</p>

<h2>📌 SDLC Lifecycle Overview</h2>

<img src="./sdlc.png" alt="SDLC Diagram" width="500"/>

<p><strong>Software Development Life Cycle (SDLC)</strong> outlines the standard phases of application development from idea to maintenance. It includes:</p>
<ol>
  <li>Planning</li>
  <li>Requirement Analysis</li>
  <li>Design</li>
  <li>Implementation</li>
  <li>Testing</li>
  <li>Deployment</li>
  <li>Maintenance</li>
</ol>

<hr>

<h2>⚙️ DevOps Infinity Loop</h2>

<img src="./devops.png" alt="DevOps Infinity Loop" width="600"/>

<p><strong>DevOps</strong> brings automation, monitoring, feedback, and agility to SDLC. It’s a culture that emphasizes collaboration between Development and Operations through CI/CD, cloud, observability, and security tools.</p>

<p>Common DevOps Stages:</p>
<ul>
  <li>Plan</li>
  <li>Develop</li>
  <li>Build</li>
  <li>Test</li>
  <li>Release</li>
  <li>Deploy</li>
  <li>Operate</li>
  <li>Monitor</li>
</ul>

<hr>

<h2>🛠️ DevOps Tools by Stage</h2>

<table border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th>DevOps Stage</th><th>Tools / Technologies</th></tr>
  </thead>
  <tbody>
    <tr><td>Plan</td><td>Jira, Azure Boards, GitHub Projects, Notion</td></tr>
    <tr><td>Develop</td><td>Spring Boot ✅, Python ✅, GitHub, GitLab, Git CLI</td></tr>
    <tr><td>Build</td><td>Maven ✅, Jenkins ✅, GitHub Actions ✅, Azure DevOps ✅</td></tr>
    <tr><td>Test</td><td>Postman, JUnit, SonarQube ✅, Trivy ✅, OWASP ZAP</td></tr>
    <tr><td>Release</td><td>GitHub Actions ✅, Jenkins Pipelines ✅, Argo CD ✅</td></tr>
    <tr><td>Deploy</td><td>Kubernetes ✅, Helm ✅, Terraform ✅, Ansible ✅, Istio ✅</td></tr>
    <tr><td>Operate</td><td>Prometheus ✅, Grafana ✅, ELK, Splunk ✅</td></tr>
    <tr><td>Monitor</td><td>OpenTelemetry ✅, Quantum Metric ✅, Azure Monitor, CloudWatch</td></tr>
  </tbody>
</table>

<hr>

<h2>🧩 Cloud & Messaging Technologies</h2>

<table border="1" cellspacing="0" cellpadding="6">
  <thead>
    <tr><th>Category</th><th>Technologies</th></tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Cloud Providers</strong></td>
      <td>
        Azure ✅ (AKS, Key Vault, DevOps, Front Door),<br/>
        AWS ✅ (EKS, EC2, CloudFront),<br/>
        KIND ✅ (for local dev), GCP, OpenShift
      </td>
    </tr>
    <tr>
      <td><strong>Messaging Brokers</strong></td>
      <td>Kafka ✅, Zookeeper ✅, RabbitMQ, ActiveMQ</td>
    </tr>
    <tr>
      <td><strong>Databases</strong></td>
      <td>MySQL ✅, MongoDB ✅, Redis ✅, Oracle DB, MS SQL Server, Cosmos DB</td>
    </tr>
    <tr>
      <td><strong>Security & QA</strong></td>
      <td>Trivy ✅, SonarQube ✅, OWASP, Snyk, Checkov</td>
    </tr>
    <tr>
      <td><strong>Monitoring & Observability</strong></td>
      <td>Prometheus ✅, Grafana ✅, OpenTelemetry ✅, ELK, Splunk ✅</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>✅ Our Project Toolchain</h2>

<ul>
  <li><strong>Languages:</strong> Spring Boot, Python</li>
  <li><strong>Messaging:</strong> Kafka, Zookeeper</li>
  <li><strong>Database:</strong> MySQL, Redis, MongoDB</li>
  <li><strong>CI/CD:</strong> Jenkins, GitHub Actions, ArgoCD</li>
  <li><strong>Security:</strong> SonarQube, Trivy, OWASP</li>
  <li><strong>Containerization:</strong> Docker, Docker Hub, Harbor</li>
  <li><strong>Deployment:</strong> Kubernetes (Kind, AKS, EKS), Helm, Terraform, Ansible</li>
  <li><strong>Monitoring:</strong> Prometheus, Grafana, Splunk, OpenTelemetry</li>
</ul>

<hr>

<h2>📚 Summary</h2>

<p>
  This documentation provides a unified view of SDLC and DevOps lifecycle, aligned with modern tools and best practices. It demonstrates how our project simulates a real-world microservices deployment pipeline using:
  <strong>CI/CD + Containers + Cloud + Security + Monitoring</strong>
</p>

<p><strong>See Also:</strong></p>
<ul>
  <li><a href="https://github.com/praveen581348/project_allinone">README.md – Project Introduction</a></li>
  <li><a href="./application_flow.md">application_flow.md – Microservices Architecture</a></li>
</ul>
