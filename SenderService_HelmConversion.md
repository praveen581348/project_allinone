<!-- 🧾 Sender Service Helm Conversion Documentation -->

<h1 align="center">🚀 Converting Sender Service Manifests into a Helm Chart</h1>

<p align="center">
  <b>Complete Step-by-Step Documentation</b><br>
  <i>From static YAML manifests to a fully reusable, parameterized Helm chart</i>
</p>

---

<h2>📝 Overview</h2>

<p>
This guide documents the full process of converting your static <code>deployment.yaml</code> and <code>service.yaml</code> files into a reusable, configurable <b>Helm chart</b> for the <code>Sender Service</code>.
</p>

---

<h2>📦 Phase 1: Chart Creation and Scaffolding</h2>

<h3>1️⃣ Create the Chart</h3>

```bash
helm create senderservice-chart
```

<h3>2️⃣ Clean Up Unused Files</h3>
<p>We removed default files that weren’t needed for this service (Ingress, HPA, and tests):</p>

```bash
rm senderservice-chart/templates/ingress.yaml
rm senderservice-chart/templates/hpa.yaml
rm -rf senderservice-chart/templates/tests
```

---

<h2>⚙️ Phase 2: Parameterization — <code>values.yaml</code></h2>

<p>
All hard-coded values from the manifests were moved into <code>senderservice-chart/values.yaml</code>.
This made the deployment configurable and reusable.
</p>

<h3>✅ Final <code>values.yaml</code></h3>

```yaml
replicaCount: 1

image:
  repository: praveen581348/senderservice
  tag: "23" # This can be overridden during deployment
  pullPolicy: IfNotPresent

service:
  type: NodePort
  port: 8098
  targetPort: 8098
  nodePort: 30098

namespace: sender

env:
  - name: SERVER_ADDRESS
    value: "0.0.0.0"   # CRITICAL FIX
  - name: SPRING_PROFILES_ACTIVE
    value: "kubernetes"
  - name: SPRING_KAFKA_BOOTSTRAP_SERVERS
    value: "kafka.messaging.svc.cluster.local:9092"

resources: {}
```

---

<h2>🧩 Phase 3: Templating the Kubernetes Files</h2>

<p>This phase replaced static values with Helm template variables and added required label helpers.</p>

---

<h3>1️⃣ <code>service.yaml</code> — Fix for Prometheus</h3>

<p>The addition of <code>name: http</code> was crucial for Prometheus <code>ServiceMonitor</code> compatibility.</p>

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "senderservice.fullname" . }}-service
  namespace: {{ .Values.namespace | default .Release.Namespace }}
  labels:
    {{- include "senderservice.labels" . | nindent 4 }}
spec:
  type: {{ .Values.service.type }}
  selector:
    {{- include "senderservice.selectorLabels" . | nindent 4 }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: {{ .Values.service.targetPort }}
      name: http   # CRITICAL FIX
      {{- if eq .Values.service.type "NodePort" }}
      nodePort: {{ .Values.service.nodePort }}
      {{- end }}
```

---

<h3>2️⃣ <code>deployment.yaml</code> — Fix for Selectors</h3>

<p>We used Helm’s label helpers to prevent the immutable field error during upgrades.</p>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "senderservice.fullname" . }}-deployment
  namespace: {{ .Values.namespace | default .Release.Namespace }}
  labels:
    {{- include "senderservice.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "senderservice.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      labels:
        {{- include "senderservice.selectorLabels" . | nindent 8 }}
    spec:
      containers:
        - name: {{ include "senderservice.name" . }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - containerPort: {{ .Values.service.targetPort }}
          env:
            {{- range .Values.env }}
            - name: {{ .name }}
              value: "{{ .value }}"
            {{- end }}
          {{- with .Values.resources }}
          resources:
            {{- toYaml . | nindent 12 }}
          {{- end }}
```

---

<h3>3️⃣ <code>_helpers.tpl</code> — Template Fixes</h3>

<p>Defined reusable label templates and naming conventions for the chart.</p>

```yaml
{{/*
Define chart name
*/}}
{{- define "senderservice.name" -}}
{{ .Chart.Name }}
{{- end -}}

{{/*
Define a fully qualified name
*/}}
{{- define "senderservice.fullname" -}}
{{ .Release.Name }}-{{ .Chart.Name }}
{{- end -}}

{{/*
Chart Name and Version
*/}}
{{- define "senderservice.chart" -}}
{{- printf "%s-%s" .Chart.Name .Chart.Version | replace "+" "_" | trunc 63 | trimSuffix "-" -}}
{{- end }}

{{/*
Selector labels
*/}}
{{- define "senderservice.selectorLabels" -}}
app.kubernetes.io/name: {{ include "senderservice.name" . }}
app.kubernetes.io/instance: {{ .Release.Name }}
{{- end }}

{{/*
Common labels
*/}}
{{- define "senderservice.labels" -}}
helm.sh/chart: {{ include "senderservice.chart" . }}
{{ include "senderservice.selectorLabels" . }}
{{- if .Chart.AppVersion }}
app.kubernetes.io/version: {{ .Chart.AppVersion | quote }}
{{- end }}
app.kubernetes.io/managed-by: {{ .Release.Service }}
{{- end }}
```

---

<h2>🧰 Phase 4: Deployment & Troubleshooting</h2>

<h3>📦 Install the Helm Chart</h3>

```bash
helm install senderservice ./senderservice-chart -n sender
```

<h3>⚠️ Fix Immutable Field Error</h3>

```bash
kubectl delete deployment senderservice-senderservice-deployment -n sender
helm upgrade senderservice ./senderservice-chart -n sender
```

---

<h3>✅ Final Verification</h3>

```bash
kubectl get pods -n sender
```

---

<h2 align="center">🎯 Final Result</h2>

<p align="center">
✅ Helm chart deployed successfully <br>
✅ Prometheus scraping working (via <code>name: http</code>) <br>
✅ Dynamic configuration via <code>values.yaml</code> <br>
✅ No more immutable field errors during upgrades <br>
✅ Ready for CI/CD integration 🎉
</p>

---

<h3 align="center">📘 Author:</h3>

<p align="center">
<b>Praveen</b> — Sender Service Helm Conversion Documentation  
<small>Generated and verified with <code>Helm v3</code> on Kubernetes</small>
</p>
