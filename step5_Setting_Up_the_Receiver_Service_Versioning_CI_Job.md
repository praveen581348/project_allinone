<div style="padding:20px; border:1px solid #ddd; border-radius:8px;">

<h1 style="color:#2c3e50; font-size:32px; font-weight:700;">
Setting Up the Receiver Service Versioning CI Job
</h1>

<h2 style="color:#2980b9;">Objective</h2>

<p>
The goal of this pipeline is to automatically trigger whenever changes are pushed to the 
<code>dev</code> branch of the <code>receiverservice</code> repository. It then determines the correct 
version from the <code>pom.xml</code> file, generates a unique Git tag based on that version, and 
pushes the tag back to the GitHub repository.
</p>

<hr/>

<h2 style="color:#8e44ad;">Prerequisites</h2>

<ul style="line-height:1.7;">
<li><strong>Jenkins:</strong> A Jenkins server is set up and running.</li>
<li><strong>GitHub Repository:</strong> A public GitHub repository for the <code>receiverservice</code> exists.</li>
<li><strong>Credentials:</strong>  
A GitHub personal access token has been added as a "Username with password" credential in Jenkins  
(ID: <code>git-creds</code>) to allow pushing tags back to the repo.</li>
<li><strong>Plugins:</strong>  
The "GitHub" and "Pipeline: GitHub" plugins are installed in Jenkins to support webhooks and status checks.</li>
<li><strong>GitHub Webhook:</strong>  
A webhook is configured in the GitHub repository to send push events to your Jenkins server’s  
<code>/github-webhook/</code> endpoint.</li>
<li><strong>Pipeline Script:</strong>  
A <code>Jenkinsfile.versioning</code> file is present in the root of the <code>dev</code> branch.</li>
</ul>

<hr/>

<h2 style="color:#d35400;">Jenkins Job Setup</h2>

<ol style="line-height:1.8;">

<li>
<strong>Job Type:</strong>  
A new “Pipeline” job named <code>receiverservice-version-generator</code> was created.
</li>

<li>
<strong>Build Trigger:</strong>  
The job is configured with the <em>GitHub hook trigger for GITScm polling</em> option enabled.  
This allows it to be automatically started by the webhook from GitHub.
</li>

<li>
<strong>Pipeline Definition:</strong>
<ul>
<li><strong>Definition:</strong> The pipeline is defined using <em>Pipeline script from SCM</em>.</li>
<li><strong>SCM:</strong> Git</li>
<li><strong>Repository URL:</strong>  
<code>https://github.com/praveen581348/receiverservice.git</code></li>
<li><strong>Credentials:</strong>  
Since the repo is public, no credentials are selected (<code>- none -</code>).</li>
<li><strong>Branch:</strong> <code>*/dev</code></li>
<li><strong>Script Path:</strong> <code>Jenkinsfile.versioning</code></li>
</ul>
</li>

</ol>

<hr/>

<h2 style="color:#27ae60;">Pipeline Logic (<code>Jenkinsfile.versioning</code>)</h2>

<p>The pipeline script performs the following steps:</p>

<ol style="line-height:1.8;">

<li>
<strong>Set GitHub Status:</strong><br/>
At the start of the pipeline, it sets the commit status on GitHub to <em>“Pending”</em>.
</li>

<li>
<strong>Checkout Code:</strong><br/>
It checks out the code from the <code>dev</code> branch using the configured Git settings.
</li>

<li>
<strong>Read & Determine Version:</strong>
<ul>
<li>It runs a shell script to extract the project version from the <code>pom.xml</code> file.</li>
<li>It determines whether the build is <code>SNAPSHOT</code> or <code>RELEASE</code>.</li>
<li>It constructs a unique tag name:
<pre><code>${buildType}-${version}-${BUILD_NUMBER}
</code></pre>
Example:
<pre><code>SNAPSHOT-0.0.1-SNAPSHOT-15</code></pre>
</li>
</ul>
</li>

<li>
<strong>Create & Push Tag:</strong>
<ul>
<li>Configures Git username and email.</li>
<li>Creates the new tag locally.</li>
<li>Pushes the tag to GitHub using <code>git-creds</code>.</li>
</ul>
</li>

<li>
<strong>Update GitHub Status:</strong><br/>
Finally, the pipeline updates commit status to either <em>Success</em> or <em>Failure</em>.
</li>

</ol>

<hr/>

<h3 style="color:#34495e; font-weight:700;">
👉 Repeat the same steps and configuration for the <strong>sender service</strong>.
</h3>

</div>
