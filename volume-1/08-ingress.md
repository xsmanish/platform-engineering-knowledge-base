# Ingress Controllers

# Executive Summary

Ingress Controllers route external traffic into Kubernetes.

---

# Architecture

Internet

↓

Ingress Controller

↓

Service

↓

Pod

---

# Types

## NGINX

Most common.

---

## AWS Load Balancer Controller

EKS.

---

## HAProxy

High performance.

---

## Traefik

Cloud native.

---

## Kong

API Gateway.

---

## Istio Gateway

Service mesh.

---

# Troubleshooting

kubectl get ingress -A

kubectl get ingressclass

kubectl describe ingress <name>

---

# Reverse Engineer

Questions:

- Which ingress controller is used?

- Is ALB created?

- Which DNS is configured?

---

# Interview Questions

1. Explain Ingress architecture.

2. NGINX vs ALB Controller?

3. Kong vs Traefik?

4. Ingress vs Istio?

---

# 30 Second Interview Answer

Ingress Controllers expose Kubernetes applications externally and route traffic to Services and Pods.