<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#2F80ED;">
  🚀 DevOps & Cloud Microservices Lab
</h1>

<p align="center" style="font-size:18px; color:#444;">
  <strong>A phase-wise real-time project that demonstrates how modern applications are built, deployed, secured, and monitored using a full spectrum of DevOps, Cloud, and container orchestration tools.</strong>
</p>

<hr style="border:1px solid #ddd;"/>

<h3 style="color:#27AE60;">🧩 Goal</h3>
<p style="font-size:16px; color:#333;">
  Build an <strong>end-to-end microservices-based architecture</strong> to understand how real-time systems function at scale using modern <strong>DevOps</strong> and <strong>Cloud-native</strong> practices.
</p>

<h3 style="color:#F39C12;">🎯 Overall Intention</h3>
<p style="font-size:16px; color:#333;">
  The overall intention of this project is to gain <strong>hands-on experience</strong> with a wide range of <strong>industry-standard technologies</strong> used in real-time production environments.<br/>
  The project is built completely <strong>from scratch</strong> in multiple phases to deeply understand the working of each technology and its real-world application.
</p>

<!-- =================================================================== -->
<!-- ================== NEW PROJECT NAVIGATION SECTION ================= -->
<!-- =================================================================== -->
<hr style="border:1px dashed #ccc; margin-top:30px; margin-bottom:30px;"/>

<h2 align="center" style="color:#3498DB;">🧭 Project Navigation</h2>
<p align="center" style="color:#555;">Start your journey here. These documents provide the foundational knowledge and architecture for the project.</p>

<div style="background-color:#f6f8fa; padding: 20px; border-radius: 8px; border: 1px solid #e1e4e8; margin: 20px 0;">
  <ul style="list-style-type: none; padding-left: 0;">
    <li style="margin-bottom: 15px;">
      <strong style="font-size: 18px;">
        <!-- !!! UPDATE THIS LINK to point to your SDLC overview file -->
        <a href="https://github.com/praveen581348/project_allinone/blob/master/SDLC-and-DevOps-Overview.md" style="text-decoration:none; color:#0366d6;">
          📘 1. Understanding the 'Why': SDLC, Cloud & DevOps
        </a>
      </strong><br/>
      <span style="font-size:15px; color:#586069;">Begin with the core concepts. Learn about the modern Software Development Lifecycle and the roles of DevOps and the Cloud.</span>
    </li>
    <li style="margin-bottom: 15px;">
  <strong style="font-size: 18px;">
    <a href="https://github.com/praveen581348/project_allinone/blob/master/application_flow.md" style="text-decoration:none; color:#0366d6;">
      🗺️ 2. Application Architecture & Flow
    </a>
  </strong><br/>
  <span style="font-size:15px; color:#586069;">Explore the end-to-end architecture, data flow, and interactions between all microservices and databases.</span>
</li>

<li>
  <strong style="font-size: 18px;">
    <a href="https://github.com/praveen581348/project_allinone/blob/master/why_docker_kubernetes_kind.md" style="text-decoration:none; color:#0366d6;">
      ☸️ 3. Why Docker, Kubernetes & kind?
    </a>
  </strong><br/>
  <span style="font-size:15px; color:#586069;">Understand the container orchestration landscape, why we chose Kubernetes, and how kind helps us prototype the full system locally in Phase 1.</span>
</li>

  </ul>
</div>

<hr style="border:1px dashed #ccc; margin-top:30px; margin-bottom:30px;"/>
<!-- =================================================================== -->
<!-- ===================== END OF NAVIGATION SECTION =================== -->
<!-- =================================================================== -->


<h2 align="center" id="project-plan" style="color:#16A085;">📅 Project Plan & Syllabus</h2>

<p style="font-size:16px; color:#333; text-align:center;">
  The project will be built in a <strong>phase-wise</strong> approach, simulating the lifecycle of real-world application development, deployment, and operations.
</p>

<ol style="font-size:16px; line-height:1.7;">

  <!-- ========== PHASE 1 ========== -->
  <li style="margin-bottom:25px;">
    <strong style="color:#E67E22; font-size:18px;">Phase 1 – Foundational Microservices & DevOps Toolchain</strong><br/>
    This phase lays the foundation by building a real-world, end-to-end microservices architecture using core backend services and a complete DevOps toolchain deployed to Kubernetes.
    <ul style="margin-top:10px;">
      <li>⚙️ <strong>Build Three Services:</strong> Two Spring Boot and one Python microservice.</li>
      <li>🛢️ <strong>Integrate Databases:</strong> MySQL, MongoDB, and Redis.</li>
      <li>🔁 <strong>Messaging & Queueing:</strong> Apache Kafka + Zookeeper.</li>
      <li>📦 <strong>Containerization:</strong> Dockerize all services.</li>
      <li>📤 <strong>Artifact Management:</strong> Push images to Nexus & Harbor.</li>
      <li>☸️ <strong>Orchestration & GitOps:</strong> Deploy to Kubernetes using Helm and automate with ArgoCD.</li>
      <li>🔧 <strong>CI/CD Pipelines:</strong> Build automated pipelines with Jenkins.</li>
      <li>🔐 <strong>Security & DevSecOps:</strong> Integrate Trivy, SonarQube, and OWASP Dependency Check.</li>
      <li>📈 <strong>Monitoring & Observability:</strong> Set up Prometheus, Grafana, and Splunk.</li>
    </ul>
  </li>

  <!-- ========== PHASE 2 ========== -->
  <li style="margin-bottom:25px;">
    <strong style="color:#2980B9; font-size:18px;">Phase 2 – Azure Cloud Integration</strong><br/>
    Migrate microservices to Azure Cloud. Use Azure Kubernetes Service (AKS) for orchestration and integrate core Azure services.
    <ul>
      <li>Azure VM, VNet, NSG, Public IP</li>
      <li>Azure Load Balancer & Application Gateway</li>
      <li>Azure Front Door & Traffic Manager</li>
      <li>Azure Key Vault (for secrets) & Azure DevOps for CI/CD</li>
    </ul>
  </li>

  <!-- ========== PHASE 3 ========== -->
  <li style="margin-bottom:25px;">
    <strong style="color:#27AE60; font-size:18px;">Phase 3 – AWS Cloud Integration</strong><br/>
    Extend or migrate the deployment to AWS Cloud. Use Elastic Kubernetes Service (EKS) and integrate with its ecosystem.
    <ul>
      <li>EC2, S3, EBS, and AWS Load Balancer</li>
      <li>AWS VPN Gateway & CloudFront for CDN</li>
      <li>AWS Systems Manager (SSM) for parameter store</li>
      <li>IAM & Secrets Manager for security</li>
    </ul>
  </li>

  <!-- ========== PHASE 4 ========== -->
  <li style="margin-bottom:25px;">
    <strong style="color:#8E44AD; font-size:18px;">Phase 4 – Advanced Enhancements</strong><br/>
    Explore advanced, production-grade concepts to improve the architecture's resilience, security, and efficiency.
    <ul>
      <li>Service Mesh (Istio or Linkerd)</li>
      <li>Multi-cluster & Hybrid Cloud Architecture</li>
      <li>Advanced Autoscaling (Horizontal/Vertical)</li>
      <li>Backup & Disaster Recovery (e.g., Velero)</li>
      <li>API Gateway (e.g., Kong or Apigee)</li>
      <li>Infrastructure as Code (Terraform or Ansible)</li>
    </ul>
  </li>
</ol>


<hr style="border:1px solid #ddd;"/>

<h3 style="color:#9B59B6;">🛠️ Complete Technology Stack</h3>
<ul style="line-height:1.8;">
  <li><strong>Programming & Backend:</strong> Spring Boot, Python</li>
  <li><strong>Databases:</strong> MySQL, MongoDB, Redis</li>
  <li><strong>Messaging:</strong> Kafka, Zookeeper</li>
  <li><strong>Containerization:</strong> Docker, DockerHub</li>
  <li><strong>Orchestration & GitOps:</strong> Kubernetes, Helm, ArgoCD</li>
  <li><strong>CI/CD:</strong> Jenkins, GitLab CI, Azure DevOps</li>
  <li><strong>Security & Scanning:</strong> SonarQube, Trivy, OWASP Dependency Check</li>
  <li><strong>Artifact Repositories:</strong> Nexus, Harbor</li>
  <li><strong>Monitoring & Logging:</strong> Prometheus, Grafana, Splunk, OpenTelemetry</li>
  <li><strong>Cloud (Azure):</strong> VM, VNet, Load Balancer, Front Door, Traffic Manager, AKS</li>
  <li><strong>Cloud (AWS):</strong> EC2, VPN, Load Balancer, CloudFront, EKS</li>
</ul>

<p style="color:#3498DB;" align="center">
  🚧 This is an evolving lab project — technologies and use cases will continue to grow over time. 🚧
</p>

</div>