# SRE OpenTelemetry Observability Project

This project demonstrates how to build a complete **observability platform** for a microservices application using Kubernetes.

The system collects **metrics, logs, and traces**, visualizes them in dashboards, defines **SLOs**, triggers **alerts**, and simulates incidents using **Game Day scenarios**.

The project runs locally on **Minikube**.

---

# Architecture Overview

The OpenTelemetry Demo application generates telemetry data which is collected and visualized by the observability stack.

                Users
                  │
                  ▼
         OpenTelemetry Demo
        (Microservices App)
                  │
                  ▼
          OpenTelemetry Collector
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
   Prometheus     Loki      Tempo
    (metrics)    (logs)    (traces)
       │          │          │
       └──────────┼──────────┘
                  ▼
                Grafana
        Dashboards • Alerts • SLOs


---

# Observability Stack

| Component | Purpose |
|---------|---------|
| Minikube | Local Kubernetes cluster |
| Prometheus | Metrics collection |
| Loki | Log aggregation |
| Tempo | Distributed tracing |
| Grafana | Visualization & dashboards |
| Alertmanager | Alert routing |
| OpenTelemetry Demo | Sample microservices application |
| k6 | Load testing |

---

# Repository Structure
.
├── app/
│   └── otel-demo
│
├── observability/
│   ├── dashboards
│   ├── alerts
│   ├── slo
│
├── loadtest/
│   └── k6
│
├── docs/
│   ├── architecture.md
│   ├── installation.md
│   ├── gameday.md
│   └── postmortems
│
└── README.md

---

# Key Features

✔ Metrics collection using Prometheus  
✔ Log aggregation with Loki  
✔ Distributed tracing with Tempo  
✔ Grafana dashboards for system visibility  
✔ Alert rules and runbooks  
✔ SLO definitions and burn rate alerts  
✔ Load testing with k6  
✔ Game Day scenarios and postmortems  

---

# Project Goals

The goal of this project is to simulate **real-world SRE practices**, including:

- Observability architecture
- Monitoring microservices
- Incident detection
- Reliability engineering
- Service Level Objectives (SLO)
- Game Day testing
- Postmortem analysis

---

# Getting Started

See:
docs/installation.md

---

# Future Improvements

- GitOps deployment using ArgoCD
- Automated alert testing
- Chaos engineering scenarios
- CI pipeline validation