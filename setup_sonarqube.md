<div style="font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';">

<h1 align="center" style="color:#2F80ED; border-bottom: 3px solid #2F80ED; padding-bottom: 10px;">
    🛡️ Deploying SonarQube for DevSecOps (Phase 1.10)
</h1>

<p align="center" style="font-size:18px; color:#444;">
    This phase deploys **SonarQube** and its dedicated **PostgreSQL** database, enabling the crucial **Quality Gate** for code analysis in our CI pipeline.
</p>

<hr style="border:1px solid #ddd;"/>

<h2>1. Kubernetes Manifests</h2>

<p>
    SonarQube requires a dedicated PostgreSQL database. We deploy both components within a new, isolated <code>sonarqube</code> namespace.
</p>

<h3>A. PostgreSQL Database Deployment & Service</h3>
<p>
    This deployment provides the persistent database backend for the SonarQube application.
</p>
<pre><code>apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: sonarqube
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: postgres:15
          env:
            # Database credentials used by SonarQube to connect
            - name: POSTGRES_DB
              value: sonarqube
            - name: POSTGRES_USER
              value: sonar
            - name: POSTGRES_PASSWORD
              value: sonarpass
          ports:
            - containerPort: 5432
          volumeMounts:
            - name: postgres-storage
              mountPath: /var/lib/postgresql/data
      volumes:
        - name: postgres-storage
          emptyDir: {} 
---
apiVersion: v1
kind: Service
metadata:
  name: postgres
  namespace: sonarqube
spec:
  ports:
    - port: 5432
  selector:
    app: postgres
</code></pre>
<p>
    <strong>Connection Detail:</strong> The service DNS name is simply <code>postgres</code>, allowing SonarQube to connect via <code>postgres:5432</code>.
</p>

<h3>B. SonarQube Server Deployment & Service (With WSL Fix)</h3>
<p>
    This deploys the SonarQube application and exposes its UI externally via a NodePort. **The <code>SONAR_WEB_HOST</code> environment variable is necessary for access in WSL/Kind environments.**
</p>
<pre><code>apiVersion: v1
kind: Namespace
metadata:
  name: sonarqube
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sonarqube
  namespace: sonarqube
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sonarqube
  template:
    metadata:
      labels:
        app: sonarqube
    spec:
      containers:
        - name: sonarqube
          image: sonarqube:latest
          ports:
            - containerPort: 9000
          env:
            # CRITICAL: Connection string uses the internal K8s DNS
            - name: SONAR_JDBC_URL
              value: "jdbc:postgresql://postgres:5432/sonarqube"
            - name: SONAR_JDBC_USERNAME
              value: "sonar"
            - name: SONAR_JDBC_PASSWORD
              value: "sonarpass"
            
            # --- CRITICAL FIX for WSL/KIND NETWORKING ---
            # Forces the web server to bind to all interfaces (0.0.0.0) 
            # to accept forwarded traffic from the NodePort service.
            - name: SONAR_WEB_HOST
              value: "0.0.0.0" 
            # ---------------------------------------------
            
          volumeMounts:
            - name: sonarqube-data
              mountPath: /opt/sonarqube/data
            - name: sonarqube-conf
              mountPath: /opt/sonarqube/conf
            - name: sonarqube-extensions
              mountPath: /opt/sonarqube/extensions
      volumes:
        - name: sonarqube-data
          emptyDir: {}
        - name: sonarqube-conf
          emptyDir: {}
        - name: sonarqube-extensions
          emptyDir: {}
---
apiVersion: v1
kind: Service
metadata:
  name: sonarqube
  namespace: sonarqube
spec:
  type: NodePort
  selector:
    app: sonarqube
  ports:
    - port: 9000
      targetPort: 9000
      nodePort: 30090
      protocol: TCP
</code></pre>

<hr style="border:1px dashed #ccc;"/>

<h2>2. Deployment and Verification</h2>

<h3>🚀 Applying the Manifests</h3>
<p>
    Apply the PostgreSQL resources first, then SonarQube, as the database must be ready before the application starts.
</p>
<pre><code># 1. Create the namespace
kubectl apply -f sonarqube-namespace.yaml

# 2. Deploy PostgreSQL (Database)
kubectl apply -f postgres-deployment-service.yaml

# 3. Deploy SonarQube Server
kubectl apply -f sonarqube-deployment-service.yaml
</code></pre>

<h3>🔍 Verification Checklist</h3>
<ol>
    <li>**Pod Status:** Check both pods are <code>Running</code>. SonarQube initialization takes time.
        <pre><code>kubectl get pods -n sonarqube</code></pre>
    </li>
    <li>**Access UI:** Once logs confirm **"Web Server is operational"** (after ~2-5 mins), access the UI via the exposed NodePort.
        <pre><code>Access URL (Recommended): http://&lt;WSL_IP_ADDRESS&gt;:30090/</code></pre>
        <p><em>Note: If <code>localhost</code> fails, use your explicit WSL IP address due to host routing issues.</em></p>
    </li>
    <li>**Initial Login:** Log in with the default credentials (Username: <code>admin</code> / Password: <code>admin</code>) and change the password. You are now ready to generate a project token and integrate the scanner into Jenkins. 
    </li>
</ol>

</div>