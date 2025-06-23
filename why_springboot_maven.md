<h1> Why Java Spring Boot + Maven</h1>

<p align="center">
  <img src="springboot.png" alt="Spring Boot Architecture" width="600"/>
</p>

<h2>🏗️ Why Learn Spring Boot as a DevOps/Cloud/SRE Engineer?</h2>
<p>
In our application architecture, we use <strong>Spring Boot</strong> and <strong>Maven</strong> to build and run two major services:
</p>
<ul>
  <li>✅ <code>senderservice</code></li>
  <li>✅ <code>receiverservice</code></li>
</ul>

<p>
These services form the core of our backend system, running in containers, communicating through Kafka, using MySQL/Redis, and deployed in Kubernetes clusters. As DevOps, SRE, or Cloud Platform Engineers:
</p>

<blockquote>
⚠️ While we may <strong>not need to write application code</strong>, it’s important to understand how the system works end-to-end — including how services are built, configured, and started.
</blockquote>

<h3>🔍 Why this knowledge matters:</h3>
<ul>
  <li>Helps with <strong>debugging build/deploy issues</strong></li>
  <li>Makes it easier to <strong>write Dockerfiles and Kubernetes manifests</strong></li>
  <li>Understand <strong>application logs, ports, configs</strong> (<code>application.properties</code>)</li>
  <li>Enables smarter use of <strong>CI/CD tools like Jenkins, ArgoCD, GitHub Actions</strong></li>
  <li>Gives context when configuring <strong>health checks, probes, metrics, secrets</strong>, etc.</li>
</ul>

<hr>

<h2>📌 What is Spring Boot?</h2>
<p>
Spring Boot is a powerful framework built on top of the Spring Framework that simplifies the development of Java-based applications. It allows developers to create <strong>standalone, production-ready applications</strong> with minimal setup.
</p>

<h2>✅ Why Spring Boot?</h2>
<table>
  <thead>
    <tr><th>Feature</th><th>Benefit</th></tr>
  </thead>
  <tbody>
    <tr><td>🚀 Auto Configuration</td><td>Automatically configures components based on classpath dependencies</td></tr>
    <tr><td>🔌 Embedded Server</td><td>No need for external Tomcat/Jetty deployment</td></tr>
    <tr><td>🧠 Convention over Config</td><td>Reduces manual XML/Java configurations</td></tr>
    <tr><td>🔐 Integrated Security</td><td>Easy setup with Spring Security</td></tr>
    <tr><td>⚙️ Production-ready</td><td>Health checks, metrics, and logging with Spring Actuator</td></tr>
    <tr><td>🧩 Microservices Ready</td><td>Best suited for distributed systems architecture</td></tr>
  </tbody>
</table>

<blockquote>
🛠️ <strong>Real-life usage</strong>: Our applications <code>senderservice</code> and <code>receiverservice</code> are developed using Spring Boot.
</blockquote>

<hr>

<h2>📁 Basic Spring Boot Project Structure</h2>

<pre>
springboot-app/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/demo/
│   │   │       └── DemoApplication.java       <-- Main class (entry point)
│   │   └── resources/
│   │       ├── application.properties         <-- App configuration
│   │       └── static/                        <-- Static assets (HTML/JS/CSS)
│   └── test/
│       └── java/                              <-- Unit tests
├── pom.xml                                    <-- Maven project definition
└── README.md
</pre>

<hr>

<h2>📦 What is Maven?</h2>
<p>
Maven is a <strong>build automation and dependency management tool</strong> for Java projects.
</p>

<h3>🔧 Why Maven?</h3>
<ul>
  <li>Manages <strong>dependencies</strong> from central repositories</li>
  <li>Handles project <strong>build lifecycle</strong></li>
  <li>Simplifies <strong>packaging</strong> (JAR/WAR)</li>
  <li>Works perfectly with Spring Boot</li>
</ul>

<hr>

<h2>🔁 Maven Lifecycle Phases</h2>

<table>
  <thead>
    <tr><th>Phase</th><th>What it does</th></tr>
  </thead>
  <tbody>
    <tr><td><code>validate</code></td><td>Validates project structure and config</td></tr>
    <tr><td><code>compile</code></td><td>Compiles the Java source code</td></tr>
    <tr><td><code>test</code></td><td>Runs unit tests</td></tr>
    <tr><td><code>package</code></td><td>Packages code into a JAR/WAR</td></tr>
    <tr><td><code>install</code></td><td>Installs package into local Maven repository</td></tr>
    <tr><td><code>deploy</code></td><td>Deploys package to a remote repository</td></tr>
  </tbody>
</table>

<pre><code>mvn clean install</code></pre>

<hr>

<h2>📄 Sample <code>pom.xml</code> (for Spring Boot)</h2>

<pre><code>&lt;project xmlns="http://maven.apache.org/POM/4.0.0"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

  &lt;groupId&gt;com.example&lt;/groupId&gt;
  &lt;artifactId&gt;demo&lt;/artifactId&gt;
  &lt;version&gt;0.0.1-SNAPSHOT&lt;/version&gt;
  &lt;packaging&gt;jar&lt;/packaging&gt;

  &lt;name&gt;springboot-demo&lt;/name&gt;

  &lt;parent&gt;
    &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
    &lt;artifactId&gt;spring-boot-starter-parent&lt;/artifactId&gt;
    &lt;version&gt;3.2.5&lt;/version&gt;
  &lt;/parent&gt;

  &lt;dependencies&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
      &lt;artifactId&gt;spring-boot-starter-web&lt;/artifactId&gt;
    &lt;/dependency&gt;
  &lt;/dependencies&gt;

  &lt;build&gt;
    &lt;plugins&gt;
      &lt;plugin&gt;
        &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
        &lt;artifactId&gt;spring-boot-maven-plugin&lt;/artifactId&gt;
      &lt;/plugin&gt;
    &lt;/plugins&gt;
  &lt;/build&gt;
&lt;/project&gt;
</code></pre>

<hr>

<h2>🌀 Spring Boot Setup using Spring Initializr</h2>
<ol>
  <li>Go to <a href="https://start.spring.io">https://start.spring.io</a></li>
  <li>Select Maven, Java, and dependencies (Spring Web)</li>
  <li>Generate the project</li>
  <li>Unzip &amp; open in IDE (IntelliJ / VSCode)</li>
</ol>

<hr>

<h2>👋 Hello World Example</h2>

<h3>📁 <code>DemoApplication.java</code></h3>

<pre><code>package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
  public static void main(String[] args) {
    SpringApplication.run(DemoApplication.class, args);
  }
}
</code></pre>

<h3>📁 <code>HelloController.java</code></h3>

<pre><code>package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

  @GetMapping("/hello")
  public String sayHello() {
    return "Hello, Spring Boot!";
  }
}
</code></pre>

<h3>📄 <code>application.properties</code></h3>

<pre><code>server.port=8080</code></pre>

<h3>▶️ Run the App</h3>
<pre><code>mvn spring-boot:run</code></pre>
<p>Open <a href="http://localhost:8080/hello">http://localhost:8080/hello</a> in browser → You will see <strong>Hello, Spring Boot!</strong></p>

<hr>

<h2>✅ Summary</h2>

<table>
  <thead>
    <tr><th>Topic</th><th>Description</th></tr>
  </thead>
  <tbody>
    <tr><td>Spring Boot</td><td>Java framework for modern applications</td></tr>
    <tr><td>Why It's Popular</td><td>Auto config, embedded servers, microservices</td></tr>
    <tr><td>Maven</td><td>Java build tool &amp; dependency manager</td></tr>
    <tr><td>Folder Structure</td><td>Clean, modular structure</td></tr>
    <tr><td>Real App Examples</td><td><code>senderservice</code>, <code>receiverservice</code></td></tr>
    <tr><td>Hello World</td><td>Demo with controller + endpoint</td></tr>
  </tbody>
</table>

<hr>

<h2>📌 Bonus: Common Maven Commands</h2>

<pre><code>
mvn clean                 # Clean previous builds
mvn compile               # Compile source code
mvn test                  # Run tests
mvn package               # Build .jar or .war file
mvn install               # Install to local Maven repo
mvn spring-boot:run       # Run Spring Boot app
</code></pre>


