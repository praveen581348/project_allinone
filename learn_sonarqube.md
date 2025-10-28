<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#2F80ED; border-bottom: 3px solid #2F80ED; padding-bottom: 10px;">
    📘 SonarQube: Continuous Code Quality Inspection
</h1>

<p align="center" style="font-size:18px; color:#444;">
    The central platform for maintaining code quality, enforcing security standards, and implementing DevSecOps practices in the CI/CD pipeline.
</p>

<hr style="border:1px solid #ddd;"/>

<h2>1. Why SonarQube? The "Shift-Left" Principle</h2>

<p>
    SonarQube integrates directly into our Continuous Integration process to catch problems as early as possible (shifting left). This dramatically reduces the cost and risk of fixing issues later.
</p>

<h3>Key Areas of Analysis</h3>
<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #f0f0f0;">
            <th style="padding: 10px; border: 1px solid #ddd;">Metric</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Goal / Impact</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Bugs</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Identifies pieces of code that will cause application failures or incorrect logic (reliability).</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Vulnerabilities</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Pinpoints security flaws (e.g., SQL Injection risks) before deployment (security).</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd;"><strong>Code Smells</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Highlights maintainability issues (e.g., overly complex functions, unused code) that increase Technical Debt.</td>
        </tr>
    </tbody>
</table>

<hr style="border:1px dashed #ccc;"/>

<h2>2. Core Concepts for CI/CD</h2>

<h3>A. Code Quality (Maintainability)</h3>
<p>
    This is SonarQube's measure of how easy your code is to manage. The key focus here is **Technical Debt**—the estimated time required to fix all current Code Smells and bring the code to compliance. Low code quality means slow future development and high maintenance costs.
</p>

<h3>B. Code Coverage (Testing Effectiveness)</h3>
<p>
    Code Coverage measures the percentage of your source code lines that are executed by automated unit tests.
</p>
<ul>
    <li><strong>How it Helps:</strong> A high percentage (e.g., >80%) provides confidence that critical application logic is tested, reducing runtime failures.</li>
    <li><strong>Integration:</strong> Tools like **JaCoCo** (for Java/Maven) generate reports during the `mvn verify` phase, which SonarQube then imports for visualization.</li>
</ul>

<h3>C. The Quality Gate (The Deployment Guardian)</h3>
<p>
    The **Quality Gate** is a set of defined criteria (pass/fail status) that a project must meet on new code before it is allowed to proceed down the pipeline. This is the cornerstone of DevSecOps.
</p>
<p>
    
</p>
<ol>
    <li>The Jenkins pipeline sends the code and reports to the SonarQube Server.</li>
    <li>SonarQube checks the results against criteria (e.g., "Must have 0 critical vulnerabilities," "New code coverage must be > 80%").</li>
    <li>If the code **FAILS** the Quality Gate, Jenkins breaks the build immediately (No-Go), preventing insecure or unstable code from reaching Nexus or Kubernetes.</li>
</ol>

<hr style="border:1px dashed #ccc;"/>

<h2>3. Integrating SonarQube in Your Pipeline</h2>

<p>
    The integration happens during the build stage of your CI pipeline (Phase 1.11, before artifact storage).
</p>
<ol>
    <li>**Deployment:** Deploy the SonarQube server to your Kubernetes cluster (e.g., exposed via NodePort <code>30090</code>, matching your <code>cluster.yaml</code>).</li>
    <li>**Build Phase:** The Jenkins job executes the build, runs unit tests, and generates code coverage reports.</li>
    <li>**Analysis Phase:** The job calls the **Sonar Scanner CLI** tool, pointing it to the local project files and the remote SonarQube server URL.</li>
    <li>**Enforcement:** Jenkins waits for the Quality Gate status before proceeding to the deployment or archiving phase in Nexus.</li>
</ol>

</div>

<hr/>
<hr/>
<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#2F80ED; border-bottom: 3px solid #2F80ED; padding-bottom: 10px;">
    📊 SonarQube Analysis Report: ReceiverService
</h1>

<p align="center" style="font-size:18px; color:#444;">
    This simulated report highlights code quality and security issues, defining the necessary **Quality Gate** for deployment in the CI/CD pipeline.
</p>

<hr style="border:1px solid #ddd;"/>

<h2>1. Quality Gate Status (The Go/No-Go Decision)</h2>

<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #f0f0f0;">
            <th style="padding: 10px; border: 1px solid #ddd;">Metric</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Status</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Threshold</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; font-weight: bold;">Reliability Rating (Bugs)</td>
            <td style="padding: 10px; border: 1px solid #ddd; color: green; font-weight: bold;">A (Pass)</td>
            <td style="padding: 10px; border: 1px solid #ddd;">Must be 'A' or 'B'</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; font-weight: bold;">Security Rating (Vulnerabilities)</td>
            <td style="padding: 10px; border: 1px solid #ddd; color: red; font-weight: bold;">B (Fail)</td>
            <td style="padding: 10px; border: 1px solid #ddd;">Must be 'A'</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; font-weight: bold;">Coverage on New Code</td>
            <td style="padding: 10px; border: 1px solid #ddd; color: red; font-weight: bold;">15% (Fail)</td>
            <td style="padding: 10px; border: 1px solid #ddd;">Must be &ge; 80%</td>
        </tr>
    </tbody>
</table>

<h3 style="color:red;">❌ QUALITY GATE FAILED: Deployment Blocked</h3>
<p>
    The build is blocked due to **Failing Security Rating** and insufficient **Code Coverage** on recent changes.
</p>

<hr style="border:1px dashed #ccc;"/>

<h2>2. Detailed Issue Breakdown</h2>

<h3>A. Vulnerabilities (Security Flaws) — 7 Cases Total</h3>
<p>
    These are high-priority issues that pose a direct security risk, especially in public-facing applications.
</p>
<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #ffe0e0;">
            <th style="padding: 10px; border: 1px solid #ddd; width: 20%;">Severity</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Issue Example</th>
            <th style="padding: 10px; border: 1px solid #ddd;">File: Line</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: red;"><strong>High (2 cases)</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">**SQL Injection** risk via unprotected string concatenation.</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>CustomRepository.java:15</code></td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: orange;"><strong>Medium (3 cases)</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Hardcoded database credentials in non-Secret configuration file.</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>application.properties:15</code></td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: lightcoral;">Low (2 cases)</td>
            <td style="padding: 10px; border: 1px solid #ddd;">Insecure cookie settings (missing <code>HttpOnly</code> flag).</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>MessageController.java:20</code></td>
        </tr>
    </tbody>
</table>

<h3>B. Bugs (Reliability Issues) — 12 Cases Total</h3>
<p>
    These are code errors that will lead to unstable behavior and runtime crashes.
</p>
<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #fff3e0;">
            <th style="padding: 10px; border: 1px solid #ddd; width: 20%;">Severity</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Issue Example</th>
            <th style="padding: 10px; border: 1px solid #ddd;">File: Line</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: red;"><strong>Critical (4 cases)</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Using floating-point variables for precise currency comparisons.</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>MessageController.java:85</code></td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: orange;"><strong>Major (3 cases)</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Null pointer exception risk when accessing Kafka message headers.</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>ReceiverService.java:42</code></td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: gold;">Minor (5 cases)</td>
            <td style="padding: 10px; border: 1px solid #ddd;">Unused exceptions caught in empty <code>try-catch</code> blocks.</td>
            <td style="padding: 10px; border: 1px solid #ddd;"><code>Mongo/client.py:102</code></td>
        </tr>
    </tbody>
</table>

<h3>C. Code Smells (Maintainability Issues) — 35 Cases Total</h3>
<p>
    These issues increase the **Technical Debt** (estimated fix time: 8 hours 10 minutes) and make the application harder to manage.
</p>
<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #e6f7ff;">
            <th style="padding: 10px; border: 1px solid #ddd; width: 20%;">Type</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Issue Example</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: #1e88e5;"><strong>Complexity</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Function complexity too high (Cognitive Complexity > 15).</td>
            <td style="padding: 10px; border: 1px solid #ddd;">5 cases</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: #1e88e5;"><strong>Duplication</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Duplicate code blocks found in API handlers.</td>
            <td style="padding: 10px; border: 1px solid #ddd;">4 cases</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: #1e88e5;"><strong>Naming</strong></td>
            <td style="padding: 10px; border: 1px solid #ddd;">Method names do not follow standard Java conventions (`camelCase`).</td>
            <td style="padding: 10px; border: 1px solid #ddd;">8 cases</td>
        </tr>
    </tbody>
</table>

<hr style="border:1px dashed #ccc;"/>

<h2>3. Code Coverage and Testing Gaps</h2>

<p>
    Low Code Coverage is the other reason the Quality Gate failed, indicating severe gaps in the unit and integration test suite.
</p>

<table style="width:100%; border-collapse: collapse; margin-bottom: 20px;">
    <thead>
        <tr style="background-color: #d1ffc9;">
            <th style="padding: 10px; border: 1px solid #ddd;">Metric</th>
            <th style="padding: 10px; border: 1px solid #ddd;">New Code Value</th>
            <th style="padding: 10px; border: 1px solid #ddd;">Issue Example</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: red;">**Line Coverage**</td>
            <td style="padding: 10px; border: 1px solid #ddd; font-weight: bold;">15.0%</td>
            <td style="padding: 10px; border: 1px solid #ddd;">The <code>saveAllToMySQL()</code> method has 0% line coverage (missing unit test).</td>
        </tr>
        <tr>
            <td style="padding: 10px; border: 1px solid #ddd; color: red;">**Branch Coverage**</td>
            <td style="padding: 10px; border: 1px solid #ddd; font-weight: bold;">10.5%</td>
            <td style="padding: 10px; border: 1px solid #ddd;">The decision logic within the `/cache-to-redis` endpoint is not covered by tests.</td>
        </tr>
    </tbody>
</table>

</div>