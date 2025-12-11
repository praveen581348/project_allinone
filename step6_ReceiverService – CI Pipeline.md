<!-- ========================================================= -->
<!--                 ReceiverService CI PIPELINE                -->
<!--        GitHub-ready Markdown with Embedded HTML            -->
<!-- ========================================================= -->

<h1 style="color:#1A73E8; font-size:34px; font-weight:700;">
📘 ReceiverService – CI Pipeline Documentation
</h1>

<p style="font-size:16px;">
A complete guide for understanding, running, and maintaining the CI pipeline 
for the <b>ReceiverService</b> application.
</p>

---

<h2 style="color:#0B8043;">📌 1. Overview</h2>

<p>
The <b>ReceiverService CI Pipeline</b> automates:
</p>

<ul>
  <li>✔ Checking out the correct Git tag for deployment</li>
  <li>✔ Building & deploying Maven artifacts (<b>DEV only</b>)</li>
  <li>✔ Building & pushing Docker images (<b>DEV only</b>)</li>
  <li>✔ Updating Helm <code>values.yaml</code> (DEV + PRD)</li>
  <li>✔ Committing changes to GitHub using GitOps workflow</li>
</ul>

<p>
It supports manual deployments using the following parameters:
</p>

<ul>
  <li><code>GIT_TAG_VERSION</code> → Exact Git tag to deploy</li>
  <li><code>ENVIRONMENT</code> → <b>dev</b> or <b>prd</b></li>
</ul>

---

<h2 style="color:#0B8043;">📌 2. CI Pipeline Flow Summary</h2>

<h3>Deployment Parameters Selected by User</h3>
<ul>
  <li><b>Tag Example:</b> <code>RELEASE-0.0.11-RELEASE-79</code>, <code>SNAPSHOT-0.0.1-SNAPSHOT-35</code></li>
  <li><b>Environment:</b> <code>dev</code> or <code>prd</code></li>
</ul>

---

<h3 style="color:#1A73E8;">🚀 DEV Deployment Workflow</h3>

<table>
<tr><th>Step</th><th>Description</th></tr>
<tr><td>Checkout dev branch</td><td>Base branch for DEV environment</td></tr>
<tr><td>Checkout Git tag</td><td>Switch repository to selected release tag</td></tr>
<tr><td>Maven build</td><td>Build JAR and push to Nexus</td></tr>
<tr><td>Docker build</td><td>Builds Docker image using Git tag</td></tr>
<tr><td>Docker push</td><td>Pushes image to DockerHub</td></tr>
<tr><td>Helm update</td><td>Updates <code>dev/values.yaml</code> with image tag</td></tr>
<tr><td>Commit & push</td><td>Updates dev branch with new Helm change</td></tr>
</table>

---

<h3 style="color:#C62828;">🛡️ PRD Deployment Workflow</h3>

<table>
<tr><th>Step</th><th>Description</th></tr>
<tr><td>Checkout master</td><td>Base branch for production</td></tr>
<tr><td>Checkout Git tag</td><td>Same process as DEV</td></tr>
<tr><td>❌ Maven build</td><td>Not required for PRD</td></tr>
<tr><td>❌ Docker build</td><td>Not required for PRD</td></tr>
<tr><td>❌ Docker push</td><td>Not required for PRD</td></tr>
<tr><td>Helm update</td><td>Updates <code>prod/values.yaml</code> with Git tag</td></tr>
<tr><td>Commit & push</td><td>Updates master branch</td></tr>
</table>

---

<h2 style="color:#0B8043;">📌 3. Jenkins Credentials Required</h2>

<table>
<tr><th>Credential ID</th><th>Purpose</th></tr>
<tr><td><code>git-creds</code></td><td>Checkout + GitHub push using PAT</td></tr>
<tr><td><code>nexus_cred</code></td><td>Upload Maven artifacts to Nexus</td></tr>
<tr><td><code>dockerhub-creds</code></td><td>Push Docker images</td></tr>
</table>

---

<h2 style="color:#0B8043;">📌 4. Jenkinsfile Explained Step-by-Step</h2>

<h3>🔹 4.1 Load Pipeline Parameters</h3>

```groovy
properties([
  parameters([
    string(name: 'GIT_TAG_VERSION', defaultValue: '', description: 'Tag to deploy'),
    choice(name: 'ENVIRONMENT', choices: ['dev', 'prd'], description: 'Target environment')
  ])
])
```

---

<h3>🔹 4.2 Global Environment Variables</h3>

```groovy
environment {
    IMAGE_NAME = "praveen581348/receiverservice"
    REPO_URL   = "https://github.com/praveen581348/receiverservice.git"
    IMAGE_TAG  = "${params.GIT_TAG_VERSION}"
}
```

Used for:
- Git operations  
- Docker image tagging  
- Helm updates  

---

<h3>🔹 4.3 Validate Input</h3>

```groovy
if (!params.GIT_TAG_VERSION?.trim()) {
    error "GIT_TAG_VERSION is required!"
}
```

Ensures safe, intentional deployments.

---

<h3>🔹 4.4 Checkout Base Branch (dev or master)</h3>

```groovy
def branchName = params.ENVIRONMENT == 'prd' ? "master" : "dev"
```

---

<h3>🔹 4.5 Checkout Git Tag</h3>

```bash
git fetch --all --tags
git reset --hard refs/tags/${IMAGE_TAG}
```

This ensures Jenkins checkout matches the tag exactly.

---

<h3 style="color:#1A73E8;">🔹 4.6 DEV: Maven Build & Nexus Deploy</h3>

Runs only for DEV:

- Updates POM version
- Builds JAR
- Pushes to Nexus

---

<h3 style="color:#1A73E8;">🔹 4.7 DEV: Docker Image Build & Push</h3>

Docker image name:

```
praveen581348/receiverservice:${IMAGE_TAG}
```

---

<h3>🔹 4.8 Update Helm Values (DEV + PRD)</h3>

File location:

```
helm/receiverservice-chart/environments/dev/values.yaml
helm/receiverservice-chart/environments/prod/values.yaml
```

Update executed by script:

```bash
./update_helm_values.sh envFolder imageTag
```

---

<h3>🔹 4.9 GitHub Commit & Push</h3>

```bash
git push origin HEAD:${targetBranch}
```

Result:
- DEV → pushes to **dev**
- PRD → pushes to **master**

---

<h2 style="color:#0B8043;">📌 5. Helm Update Example Output</h2>

```yaml
image:
  repository: praveen581348/receiverservice
  tag: RELEASE-0.0.11-RELEASE-79
```

---

<h2 style="color:#0B8043;">📌 6. Deployment Examples</h2>

<h3 style="color:#1A73E8;">✔ Example DEV Deployment</h3>

```
Tag: RELEASE-1.0.5-33
ENV: dev
```

✔ Maven Build  
✔ Docker Build  
✔ Docker Push  
✔ Helm Update  
✔ Commit to dev  

---

<h3 style="color:#C62828;">✔ Example PRD Deployment</h3>

```
Tag: RELEASE-1.0.5-33
ENV: prd
```

❌ No Maven  
❌ No Docker  
✔ Helm Update  
✔ Commit to master  

---

<h2 style="color:#0B8043;">📌 7. CI Architecture Diagram</h2>

```
              +-------------------+
    User ---> |  Jenkins Trigger  |
              +----------+--------+
                         |
                         v
            +------------+-------------+
            | Validate Parameters      |
            +------------+-------------+
                         |
                         v
           +-------------+------------------+
           | Checkout Base Branch (dev/prd) |
           +-------------+------------------+
                         |
                         v
                   Checkout Tag
                         |
           +-----------------------------+
           | DEV Environment Only        |
           | Maven Build / Docker Build  |
           | Docker Push                 |
           +-----------------------------+
                         |
                         v
               Update Helm values.yaml
                         |
                         v
                 Commit & Push to GitHub
```

---

<h2 style="color:#0B8043;">📌 8. Benefits of This CI Setup</h2>

<ul>
  <li>✔ Fully automated GitOps pipeline</li>
  <li>✔ Versioning aligned with Git tags</li>
  <li>✔ Clear DEV/PRD separation</li>
  <li>✔ Ensures safe production deployments</li>
  <li>✔ Nexus + DockerHub + Helm integrated</li>
  <li>✔ Zero manual file editing</li>
</ul>

---

<h2 style="color:#0B8043;">📌 9. Maintenance Tips</h2>

<ul>
  <li>Always create tags before deployment</li>
  <li>Never push directly to <code>master</code></li>
  <li>Keep GitHub PAT credentials updated</li>
  <li>Ensure Docker + Maven are installed on Jenkins node</li>
  <li>Do not modify Helm directory structure</li>
</ul>

---

<h2 style="color:#1A73E8;">✨ End of Documentation</h2>
