# Prometheus Deep Dive

# Executive Summary

Prometheus is a pull-based monitoring system that collects metrics and stores them in a time-series database.

---

# Architecture

Application

↓

Exporter

↓

Prometheus

↓

AlertManager

↓

Grafana

---

# Exporters

## Node Exporter

Node metrics.

Examples:

- CPU

- Memory

- Disk

---

## kube-state-metrics

Kubernetes objects.

Examples:

- Pods

- Deployments

- StatefulSets

---

## cAdvisor

Container metrics.

---

## Blackbox Exporter

Endpoint monitoring.

---

# ServiceMonitor

Discover Services.

---

# PodMonitor

Discover Pods.

---

# Recording Rules

Precompute expensive queries.

---

# Alert Rules

Trigger alerts.

---

# Troubleshooting

No metrics:

kubectl get servicemonitor -A

---

Prometheus unhealthy:

kubectl logs prometheus-0

---

No alerts:

kubectl get prometheusrule -A

---

# Reverse Engineer Your Cluster

kubectl get servicemonitor -A

kubectl get podmonitor -A

kubectl get prometheusrule -A

Questions:

- Which exporters exist?
- Which services are monitored?

---

# Interview Questions

1. Explain Prometheus architecture.

2. Pull vs Push?

3. What are exporters?

4. ServiceMonitor vs PodMonitor?

5. Recording Rules vs Alert Rules?

---

# 30 Second Interview Answer

Prometheus is a pull-based monitoring system that scrapes exporters, stores metrics, and generates alerts.