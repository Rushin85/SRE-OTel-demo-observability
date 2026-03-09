# Installation Guide

This guide describes how to deploy the full observability stack locally using **Minikube**.

The stack includes:

* Prometheus (metrics)
* Grafana (visualization)
* Alertmanager (alerting)
* Loki (logs)
* Tempo (traces)
* OpenTelemetry Demo (microservices application)

---

# Requirements

Install the following tools before starting:

* Docker
* kubectl
* Helm
* Minikube

Verify installations:

```bash
kubectl version --client
helm version
minikube version
docker version
```

---

# Start Kubernetes Cluster

Start Minikube with enough resources for the observability stack.

```bash
minikube start --driver=docker --cpus=4 --memory=8192 --disk-size=40g
```

Verify cluster:

```bash
kubectl get nodes
```

Expected output:

```
minikube   Ready
```

---

# Install Helm Repositories

Add the required Helm repositories.

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update
```

---

# Create Namespaces

Create namespaces for the observability stack and the demo application.

```bash
kubectl create namespace observability
kubectl create namespace app
```

Verify namespaces:

```bash
kubectl get ns
```

---

# Install Prometheus Monitoring Stack

Install the Kubernetes monitoring stack which includes:

* Prometheus
* Grafana
* Alertmanager
* kube-state-metrics
* node-exporter

```bash
helm upgrade --install monitoring prometheus-community/kube-prometheus-stack \
-n observability
```

Verify pods:

```bash
kubectl get pods -n observability
```

Expected components:

```
monitoring-grafana
monitoring-kube-prometheus-alertmanager
monitoring-kube-prometheus-operator
monitoring-kube-prometheus-prometheus
monitoring-kube-state-metrics
monitoring-prometheus-node-exporter
```

---

# Access Grafana

Forward the Grafana service:

```bash
kubectl port-forward -n observability svc/monitoring-grafana 3000:80
```

Open Grafana:

```
http://localhost:3000
```

---

# Get Grafana Admin Password

Retrieve the admin password:

```bash
kubectl get secret -n observability monitoring-grafana \
-o jsonpath="{.data.admin-password}" | base64 --decode && echo
```

Login credentials:

```
username: admin
password: <output from command above>
```

---

# Install Loki (Logging Backend)

Create Loki values file:

```
observability/helm-values/loki-values.yaml
```

Example configuration:

```yaml
deploymentMode: SingleBinary

loki:
  auth_enabled: false

  commonConfig:
    replication_factor: 1

  storage:
    type: filesystem
    bucketNames:
      chunks: chunks
      ruler: ruler
      admin: admin

  schemaConfig:
    configs:
      - from: "2024-01-01"
        store: tsdb
        object_store: filesystem
        schema: v13
        index:
          prefix: index_
          period: 24h

  storage_config:
    filesystem:
      directory: /var/loki/chunks

singleBinary:
  replicas: 1

read:
  replicas: 0

write:
  replicas: 0

backend:
  replicas: 0

gateway:
  enabled: false
```

Install Loki:

```bash
helm upgrade --install loki grafana/loki \
-n observability \
-f observability/helm-values/loki-values.yaml
```

Verify Loki:

```bash
kubectl get pods -n observability
```

---

# Install Tempo (Tracing Backend)

Install Tempo:

```bash
helm upgrade --install tempo grafana/tempo \
-n observability
```

Verify:

```bash
kubectl get pods -n observability
```

---

# Deploy OpenTelemetry Demo Application

Install the demo application.

```bash
helm upgrade --install otel-demo open-telemetry/opentelemetry-demo \
-n app
```

Verify deployment:

```bash
kubectl get pods -n app
```

---

# Access Demo Application

Find the frontend service:

```bash
kubectl get svc -n app
```

Port-forward the frontend:

```bash
kubectl port-forward -n app svc/otel-demo-frontendproxy 8080:8080
```

Open:

```
http://localhost:8080
```

---

# Observability Stack Overview

At this stage the following components should be running:

Metrics:

* Prometheus

Logs:

* Loki

Traces:

* Tempo

Visualization:

* Grafana

Application:

* OpenTelemetry Demo

---

# Verify Telemetry Flow

Open Grafana and confirm:

* Prometheus metrics are visible
* Loki logs are searchable
* Tempo traces are available
* dashboards display application activity

---

# Next Steps

After deployment you can:

* configure Grafana dashboards
* define alert rules
* create SLOs
* run load tests using k6
* perform Game Day failure scenarios
* write incident postmortems
