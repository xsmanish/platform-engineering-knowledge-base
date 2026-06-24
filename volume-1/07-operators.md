# Kubernetes Operators & Cert Manager Deep Dive

# Senior Platform Engineer Interview Handbook

---

# Chapter Objective

By the end of this chapter, you should be able to answer:

1. What problem do Operators solve?

2. Why do we need Operators?

3. Explain the Operator Pattern.

4. Explain CRDs.

5. Explain Controllers.

6. Explain Reconciliation Loop.

7. Why is Cert Manager implemented as an Operator?

8. Explain Issuer vs ClusterIssuer.

9. Explain certificate lifecycle.

10. How do you troubleshoot Operators?

11. How would you design certificate automation?

12. How does this connect to my experience?

---

# 1. Executive Summary (30 sec answer)

Kubernetes Operators extend Kubernetes by automating the lifecycle management of complex applications.

Operators combine:

- CRDs (Custom Resource Definitions)
- Controllers
- Reconciliation loops

to automate operational tasks that engineers would otherwise perform manually.

Think of an Operator as:

Human operational knowledge + Automation.

Examples:

- Cert Manager
- Prometheus Operator
- External Secrets Operator
- Karpenter

---

# 2. The Problem Operators Solve

Kubernetes natively manages:

- Pods
- Services
- Deployments
- StatefulSets

But Kubernetes does not know how to manage application-specific operations.

Examples:

Certificates need:

- Issue
- Renew
- Rotate

Databases need:

- Backup
- Restore
- Upgrade

Secrets need:

- Synchronize
- Rotate

Nodes need:

- Provision
- Consolidate
- Decommission

Without Operators:

Engineer

↓

Observe

↓

Detect Issue

↓

Run Commands

↓

Fix Problem

↓

Repeat Forever

Problems:

- Manual effort
- Error-prone
- Doesn't scale
- Operational burden

Operators solve this.

---

# 3. Operator Pattern

Traditional Operations

Human

↓

Monitor

↓

Detect Issue

↓

Take Action

↓

Repeat

---

Operator Pattern

Desired State

↓

Observe

↓

Compare

↓

Take Action

↓

Repeat Forever

The Operator continuously maintains the desired state.

---

# 4. Operator Architecture

                  Custom Resource Definition (CRD)
                                   |
                                   |
                          Custom Resource
                                   |
                                   |
                 +--------------------------------+
                 |       Operator Controller      |
                 +--------------------------------+
                                   |
                                   |
                       Reconciliation Loop
                                   |
                                   |
         +----------------------------------------------+
         | Kubernetes Resources                         |
         |----------------------------------------------|
         | Pods                                         |
         | Services                                     |
         | Secrets                                      |
         | Certificates                                 |
         | ConfigMaps                                   |
         +----------------------------------------------+

---

# 5. Core Concepts

## CRD (Custom Resource Definition)

Purpose:

Extend Kubernetes.

Examples:

Certificate

ClusterIssuer

Prometheus

ExternalSecret

NodePool

Think:

CRD = Blueprint

---

## Custom Resource

An instance created from a CRD.

Think:

Deployment

↓

Deployment YAML

Similarly:

Certificate

↓

Certificate YAML

---

## Controller

The brain of the Operator.

Responsibilities:

- Watch resources
- Compare state
- Execute actions

---

## Reconciliation Loop

The heart of Kubernetes Operators.

Responsibilities:

1. Observe current state

2. Compare desired state

3. Fix differences

4. Repeat forever

This loop never stops.

---

# 6. Reconciliation Loop (Most Important Concept)

Desired State

↓

Observe Current State

↓

Compare

↓

Different?

↓

YES

↓

Take Action

↓

Observe Again

↓

Repeat Forever

Interviewers LOVE this concept.

Memorize this.

---

# 7. How Operators Work

Step 1

Engineer creates a Custom Resource.

↓

Step 2

Operator watches it.

↓

Step 3

Controller detects changes.

↓

Step 4

Controller performs actions.

↓

Step 5

Desired state achieved.

↓

Step 6

Controller keeps monitoring.

---

# 8. Cert Manager Deep Dive

Purpose:

Automate certificate management.

Without Cert Manager:

Engineer

↓

Request certificate

↓

Upload Secret

↓

Track expiry

↓

Renew manually

↓

Update Ingress

↓

Repeat

Problems:

- Error-prone
- Manual effort
- Downtime risk

---

With Cert Manager:

Certificate Resource

↓

Cert Manager

↓

Certificate Authority

↓

Secret Created

↓

Ingress Uses Secret

↓

HTTPS Enabled

↓

Auto Renewal

---

# 9. Cert Manager Architecture

Application

↓

Ingress

↓

Certificate Resource

↓

Cert Manager

↓

ClusterIssuer

↓

Certificate Authority

↓

Secret

↓

HTTPS Enabled

---

# 10. Core Cert Manager Components

## Certificate

Defines:

Desired certificate.

Example:

api.company.com

---

## Issuer

Namespace scoped.

Only accessible inside one namespace.

---

## ClusterIssuer

Cluster scoped.

Accessible across all namespaces.

Most enterprises use ClusterIssuer.

---

## Certificate Authority (CA)

Issues certificates.

Examples:

- Let's Encrypt

- Internal PKI

- Venafi

---

## Secret

Stores:

tls.crt

tls.key

---

# 11. Certificate Lifecycle

Application deployed

↓

Ingress created

↓

Certificate created

↓

Cert Manager detects request

↓

ClusterIssuer contacted

↓

Certificate Authority issues certificate

↓

Secret created

↓

Ingress uses Secret

↓

HTTPS Enabled

---

# 12. Automatic Renewal Workflow

Certificate nearing expiry

↓

Cert Manager detects expiry

↓

Request new certificate

↓

Update Secret

↓

Application continues running

↓

No downtime

---

# 13. Production Usage

Typical enterprise usage:

Ingress TLS

↓

Istio Gateways

↓

Internal APIs

↓

External APIs

↓

Web Applications

↓

Service Mesh

---

# 14. Other Popular Operators

## Prometheus Operator

Resources:

- ServiceMonitor

- PodMonitor

- PrometheusRule

---

## External Secrets Operator

AWS Secrets Manager

↓

External Secret

↓

Kubernetes Secret

---

## Karpenter

Responsibilities:

- Create Nodes

- Remove Nodes

- Consolidate Nodes

---

# 15. End-To-End Workflow

Application deployed

↓

Ingress created

↓

Certificate resource created

↓

Cert Manager watches resource

↓

ClusterIssuer contacted

↓

Certificate Authority contacted

↓

Secret generated

↓

Ingress updated

↓

HTTPS Enabled

---

# 16. Troubleshooting Tree

Certificate Not Generated

↓

Is Cert Manager running?

↓

YES

↓

Is ClusterIssuer configured?

↓

YES

↓

Can Certificate Authority be reached?

↓

YES

↓

Can Secret be created?

↓

YES

↓

Certificate Ready

---

# 17. Troubleshooting Commands

List CRDs

kubectl get crd

---

Check Cert Manager Pods

kubectl get pods -n cert-manager

---

Check ClusterIssuers

kubectl get clusterissuer

---

Check Issuers

kubectl get issuer -A

---

Check Certificates

kubectl get certificate -A

---

Describe Certificate

kubectl describe certificate <name>

---

Check Secrets

kubectl get secret -A

---

Check Logs

kubectl logs deployment/cert-manager -n cert-manager

---

# 18. Reverse Engineer Your Existing Cluster

Questions to answer:

1. Is Cert Manager installed?

2. Which ClusterIssuers exist?

3. Which Certificate Authority is used?

4. Which applications use TLS?

5. Which Ingresses use TLS?

6. Which Secrets are certificate Secrets?

Commands:

kubectl get pods -n cert-manager

kubectl get clusterissuer

kubectl get certificate -A

kubectl get ingress -A

kubectl get secret -A

---

# 19. How This Connects To My Experience

NextGen Platform

↓

ArgoCD

↓

Cert Manager

↓

Ingress

↓

TLS Certificates

↓

Applications

Interview Answer:

"In our environment, Cert Manager is deployed as a platform add-on and managed through ArgoCD. It automates certificate issuance and renewal for ingress endpoints and platform services, eliminating manual certificate management."

---

# 20. Architect Level Interview Questions

Q1

Explain the Operator Pattern.

---

Q2

What problem do Operators solve?

---

Q3

What is a CRD?

---

Q4

CRD vs Custom Resource?

---

Q5

Why is Cert Manager an Operator?

---

Q6

What is a Reconciliation Loop?

---

Q7

Issuer vs ClusterIssuer?

---

Q8

How does automatic certificate renewal work?

---

Q9

How do you troubleshoot Cert Manager?

---

Q10

Which Operators have you worked with?

Answer:

- Cert Manager

- Karpenter

- External Secrets

- Prometheus Operator

---

# 21. 5 Minute Interview Story

Question:

Explain Cert Manager in your environment.

Answer:

"In our environment, Cert Manager is deployed as a Kubernetes Operator and managed through ArgoCD. It automates certificate issuance and renewal for applications exposed through ingress controllers.

Applications create Certificate resources, which are processed by Cert Manager using a ClusterIssuer. Cert Manager communicates with the Certificate Authority, generates certificates, stores them as Kubernetes Secrets, and automatically renews them before expiry.

This significantly reduces operational overhead and eliminates manual certificate management."

---

# 22. One Page Cheat Sheet

CRD

↓

Custom Resource

↓

Controller

↓

Operator

↓

Reconciliation Loop

↓

Certificate

↓

ClusterIssuer

↓

Secret

↓

Ingress

↓

HTTPS

Remember:

Kubernetes Operators = Human Expertise + Automation

Reconciliation Loop = Observe → Compare → Act → Repeat

Cert Manager = Certificate Automation Engine

ClusterIssuer = Enterprise Standard
