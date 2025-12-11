<!-- ========================================================= -->
<!--             ArgoCD SETUP GUIDE FOR KIND CLUSTER           -->
<!--        GitHub-Ready Markdown With Embedded HTML           -->
<!-- ========================================================= -->

<h1 style="color:#1A73E8; font-size:34px; font-weight:700;">
🚀 ArgoCD Setup Guide for Kind Cluster (kind-dev)
</h1>

<p style="font-size:16px;">
This guide walks you through installing <b>ArgoCD</b> into a local <b>Kind cluster</b>, 
exposing the ArgoCD UI securely over NodePort, retrieving admin credentials, 
and logging in via CLI or browser.
</p>

---

<h2 style="color:#0B8043;">📌 1. Objective</h2>

<ul>
  <li>Install ArgoCD inside <b>kind-dev</b> cluster</li>
  <li>Expose ArgoCD server externally using <b>NodePort</b></li>
  <li>Retrieve initial ArgoCD admin password</li>
  <li>Access ArgoCD dashboard using CLI or Web UI</li>
</ul>

---

<h2 style="color:#0B8043;">📌 2. Prerequisites</h2>

<ul>
  <li>A running Kind cluster named <code>kind-dev</code></li>
  <li><code>kubectl</code> configured for context <code>kind-dev</code></li>
  <li>Optional: <b>ArgoCD CLI</b> installed (for CLI login)</li>
</ul>

---

<h2 style="color:#0B8043;">📌 3. Install ArgoCD</h2>

<h3>Step 1: Create namespace and deploy ArgoCD components</h3>

```bash
# 1. Create the namespace
kubectl create namespace argocd

# 2. Apply the stable ArgoCD manifest
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

<p><b>Note:</b> The install manifest may create the namespace automatically, but adding it explicitly avoids issues.</p>

---

<h2 style="color:#0B8043;">📌 4. Expose ArgoCD Server using NodePort</h2>

<p>
By default, <code>argocd-server</code> is a <b>ClusterIP</b> service, meaning it isn't available externally.  
For local development via Kind, we expose port <b>443</b> on <b>NodePort 30101</b>.
</p>

```bash
kubectl patch svc argocd-server -n argocd \
  -p '{"spec": {"type": "NodePort", "ports": [{"name": "https", "port": 443, "targetPort": 8080, "nodePort": 30101}]}}'
```

Expected output:
```
service/argocd-server patched
```

---

<h2 style="color:#0B8043;">📌 5. Retrieve ArgoCD Initial Admin Password</h2>

<p>
ArgoCD stores the initial admin password in a Kubernetes secret.  
Extract and decode it using:
</p>

```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d && echo
```

Example output:

```
6XY2Un0dYP8H1MUY
```

<p>
⚠️ <b>Important:</b> Store this password securely.  
You should change it immediately after logging into ArgoCD.
</p>

---

<h2 style="color:#0B8043;">📌 6. Accessing ArgoCD</h2>

There are two access methods:

---

<h3 style="color:#1A73E8;">🔹 Option A: Login via ArgoCD CLI</h3>

<p>Use <code>localhost:30101</code> (NodePort we configured).</p>

```bash
argocd login localhost:30101 \
  --username admin \
  --password 6XY2Un0dYP8H1MUY \
  --insecure
```

<p>
<b>Why --insecure?</b>  
ArgoCD uses a self-signed SSL certificate, so you must bypass certificate verification.
</p>

---

<h3 style="color:#1A73E8;">🔹 Option B: Login via Web UI</h3>

<p>
Open your browser and go to:
</p>

```
https://localhost:30101
```

<p>
Accept the browser warning for the self-signed certificate.
</p>

<table>
<tr><th>Field</th><th>Value</th></tr>
<tr><td>Username</td><td><code>admin</code></td></tr>
<tr><td>Password</td><td><code>(value from Step 5)</code></td></tr>
</table>

---

<h2 style="color:#0B8043;">📌 7. Summary</h2>

<ul>
  <li>Create namespace → <code>argocd</code></li>
  <li>Install ArgoCD from official manifest</li>
  <li>Patch server service to expose NodePort (30101)</li>
  <li>Retrieve admin password</li>
  <li>Login using CLI or Web UI</li>
</ul>

---

<h2 style="color:#1A73E8; font-weight:700;">
✨ Setup Complete — ArgoCD is now ready in your Kind Cluster!
</h2>
