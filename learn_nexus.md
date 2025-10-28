<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#E67E22; border-bottom: 3px solid #E67E22; padding-bottom: 10px;">
    📘 Sonatype Nexus Repository OSS: Artifact Management
</h1>

<p align="center" style="font-size:18px; color:#444;">
    Nexus is the central "warehouse" for all software components, managing dependencies and binaries in our CI/CD pipeline.
</p>

<hr style="border:1px solid #ddd;"/>

<h2>1. Core Purpose in DevOps</h2>
<p>
    Nexus is crucial for achieving fast, reliable, and secure builds by eliminating direct reliance on slow or unstable public repositories.
</p>

<ul>
    <li><strong>Speed & Reliability:</strong> Acts as a local cache for dependencies (Maven, Docker), drastically reducing build times and improving stability.</li>
    <li><strong>Security & Governance:</strong> Centralizes artifact access, allowing us to enforce security policies and use authenticated access (RBAC) for tools like Jenkins.</li>
    <li><strong>Version Management:</strong> Provides a controlled, private location for storing all versions of our microservices (releases and snapshots).</li>
</ul>

<hr style="border:1px dashed #ccc;"/>

<h2>2. Repository Types (The Foundation)</h2>
<p>
    Nexus classifies artifacts into three repository types. Understanding this is essential for configuration:
</p>

<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #f0f0f0;">
            <th style="padding: 10px; border: 1px solid #ddd;">Type</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Function</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Use in Your Project</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Hosted</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Stores <strong>your own internal components</strong> (e.g., final JARs, Docker images).</td>
            <td style="padding: 10px; border: 1px solid #ddd;">Storing your <code>SenderService</code> and <code>ReceiverService</code> Docker images.</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Proxy</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Caches components from <strong>external public repositories</strong> (e.g., Maven Central, Docker Hub).</td>
            <td style="padding: 10px; border: 1px solid #ddd;">Your Spring Boot apps pull dependencies from this local cache during the Maven build phase.</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Group</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Combines multiple Hosted and Proxy repositories under a **single endpoint**.</td>
            <td style="padding: 10px; border: 1px solid #ddd;">Jenkins and developers use one simple URL to access everything.</td>
        </tr>
    </tbody>
</table>

<p></p>

<hr style="border:1px dashed #ccc;"/>

<h2>3. Integration with CI/CD (Jenkins Roadmap)</h2>
<p>
    Nexus is integrated directly into the automated pipeline (Phase 1.9) via specific configuration points.
</p>

<h3>Configuration Steps</h3>
<ol>
    <li><strong>Deployment:</strong> Deploy Nexus 3 OSS to Kubernetes via NodePort <code>30081</code>.</li>
    <li><strong>Repository Setup:</strong> Configure separate repositories for <code>Maven 2 (Hosted)</code> and <code>Docker (Hosted)</code>.</li>
    <li><strong>Security:</strong> Create a dedicated service user (e.g., <code>jenkins-user</code>) with limited **Write/Deploy** privileges to the Hosted repositories.</li>
    <li><strong>Maven Integration:</strong> Configure the Maven build (via <code>pom.xml</code> or Jenkins settings) to deploy the Spring Boot JARs to the Nexus Maven Hosted URL.</li>
    <li><strong>Docker Integration:</strong> The Jenkins pipeline must perform a `docker login` using the service user credentials to authenticate against the Nexus Docker Hosted registry before executing `docker push`.</li>
</ol>

<p>
    This integration ensures that all build artifacts are secured and centralized before being pulled for final deployment to Kubernetes.
</p>

</div>