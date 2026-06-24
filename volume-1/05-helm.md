# Helm Deep Dive

# Senior Platform Engineer Interview Handbook

---

# Chapter Objective

By the end of this chapter, you should be able to answer:

1. Why do we need Helm?

2. Explain Helm architecture.

3. Explain Chart.yaml.

4. Explain values.yaml.

5. Explain templates.

6. Explain _helpers.tpl.

7. Explain Helm dependencies.

8. Explain Helm release lifecycle.

9. Explain Helm vs Kustomize.

10. How do you troubleshoot Helm deployments?

11. How is Helm used in production?

12. How does this relate to my current experience?

---

# 1. Executive Summary (30 sec answer)

Helm is a package manager for Kubernetes.

It allows applications to be packaged, templated, versioned, and deployed consistently.

Instead of maintaining hundreds of YAML files, Helm uses templates and variables to generate Kubernetes manifests dynamically.

Helm's primary responsibilities are:

* Package applications
* Templatize configurations
* Manage releases
* Handle upgrades
* Support rollbacks
* Manage dependencies

---

# 2. The Problem Helm Solves

Without Helm:

Application

↓

100+ YAML files

↓

Different environments

↓

Duplicate configurations

↓

Manual changes

↓

Configuration drift

Problems:

* Duplicate manifests
* Difficult environment management
* Difficult upgrades
* Difficult rollbacks
* Difficult versioning

---

With Helm:

Chart

↓

Templates

↓

Values

↓

Rendered YAML

↓

Kubernetes

Benefits:

* Reusability
* Consistency
* Version control
* Easy upgrades
* Easy rollbacks

---

# 3. Helm Architecture

Developer

↓

Helm CLI

↓

Chart

↓

Values

↓

Template Engine

↓

Rendered YAML

↓

Kubernetes API Server

↓

Kubernetes Cluster

Helm itself does not run continuously.

It renders manifests and sends them to Kubernetes.

---

# 4. Core Components

## Helm CLI

Purpose:

User interface.

Commands:

* install
* upgrade
* rollback
* uninstall

---

## Chart

Purpose:

Package an application.

Contains:

* Metadata
* Templates
* Variables
* Dependencies

---

## Template Engine

Purpose:

Render templates.

Input:

templates + values

Output:

YAML

---

## Release

Purpose:

Running instance of a chart.

Think:

Chart + Environment = Release

Example:

payments-prod

payments-dev

payments-qa

---

# 5. Chart Structure

my-chart/

Chart.yaml

values.yaml

values-dev.yaml

values-prod.yaml

templates/

_helpers.tpl

charts/

README.md

---

# 6. Chart.yaml

Purpose:

Metadata.

Example:

apiVersion: v2

name: payment-service

description: Payment application

version: 1.0.0

appVersion: 2.1.0

Fields:

name

version

description

type

dependencies

---

# 7. values.yaml

Purpose:

Input variables.

Example:

replicaCount: 3

image:

repository: nginx

tag: 1.25

service:

type: ClusterIP

Environment-specific values:

values-dev.yaml

values-qa.yaml

values-prod.yaml

---

# 8. templates/

Contains Kubernetes manifests.

Examples:

deployment.yaml

service.yaml

configmap.yaml

ingress.yaml

hpa.yaml

serviceaccount.yaml

---

# 9. _helpers.tpl

Purpose:

Reusable functions.

Example:

{{ include "fullname" . }}

Use cases:

* Naming conventions
* Labels
* Selectors

Interview keyword:

Reusable template library

---

# 10. Helm Dependencies

Purpose:

Install charts inside charts.

Example:

application-chart

↓

dependencies

↓

postgres

↓

redis

↓

nginx

Chart.yaml:

dependencies:

* name: redis

* name: postgresql

Benefits:

* Modularity
* Reusability

---

# 11. Helm Release Lifecycle

helm install

↓

helm upgrade

↓

helm history

↓

helm rollback

↓

helm uninstall

Every operation creates a new release revision.

---

# 12. End-To-End Workflow

Developer

↓

Update values.yaml

↓

Git Commit

↓

ArgoCD

↓

Repo Server

↓

Helm Render

↓

Manifest Generation

↓

Kubernetes API Server

↓

Resources Created

---

# 13. Helm Template Rendering

Template:

image:

repository: {{ .Values.image.repository }}

tag: {{ .Values.image.tag }}

Values:

repository: nginx

tag: 1.25

Rendered YAML:

image:

repository: nginx

tag: 1.25

---

# 14. Production Usage

Typical Helm deployments:

Platform Add-ons

├── Istio

├── Cert Manager

├── Fluent Bit

├── Dynatrace

├── EBS CSI Driver

├── External Secrets

├── Karpenter

└── Application Workloads

---

# 15. Helm vs Kustomize

Helm

Package Manager

Templates

Dependencies

Versioning

Releases

Rollback

Variables

---

Kustomize

Overlay Tool

Native Kubernetes

No release management

No package management

No rollback

No dependencies

Interview answer:

Helm is used for packaging applications.

Kustomize is used for overlaying configurations.

---

# 16. Common Helm Commands

Install

helm install app ./chart

---

Upgrade

helm upgrade app ./chart

---

History

helm history app

---

Rollback

helm rollback app 1

---

Uninstall

helm uninstall app

---

Lint

helm lint .

---

Render

helm template .

---

# 17. Troubleshooting Tree

Helm Deployment Failed

↓

Can templates render?

↓

YES

↓

Are values correct?

↓

YES

↓

Can Kubernetes create resources?

↓

YES

↓

Resource Healthy

---

# 18. Troubleshooting Commands

List releases

helm list -A

---

Get values

helm get values <release>

---

Get manifests

helm get manifest <release>

---

Get history

helm history <release>

---

Rollback

helm rollback <release> 1

---

Check templates

helm template .

---

Lint chart

helm lint .

---

# 19. Reverse Engineer Your Existing Cluster

Questions to answer:

1. Which charts are installed?

2. Which namespaces use Helm?

3. Which values differ by environment?

4. Which platform add-ons use Helm?

5. Are dependencies used?

6. Which repositories are configured?

Commands:

helm list -A

helm get values <release>

helm get manifest <release>

helm history <release>

helm repo list

---

# 20. How This Connects To My Experience

NextGen Platform

↓

GitHub

↓

CodePipeline

↓

Docker Build

↓

ECR

↓

Helm Charts

↓

ArgoCD

↓

EKS

↓

Applications

Interview answer:

"In our environment, applications are packaged as Helm charts. Helm templates generate Kubernetes manifests, and ArgoCD consumes those charts from Git repositories to deploy workloads into EKS clusters."

---

# 21. Architect Level Interview Questions

Q1

Explain Helm architecture.

---

Q2

What is a Helm release?

---

Q3

What is _helpers.tpl?

---

Q4

How does values.yaml work?

---

Q5

How does rollback work?

---

Q6

How do dependencies work?

---

Q7

Helm vs Kustomize?

---

Q8

How do you structure Helm charts for multiple environments?

---

Q9

How do you troubleshoot failed releases?

---

Q10

How does ArgoCD integrate with Helm?

---

# 22. 5 Minute Interview Story

Question:

Explain Helm usage in your environment.

Answer:

"In our platform, Helm acts as the packaging layer for Kubernetes applications. Application teams define reusable templates, environment-specific values, and dependencies inside Helm charts.

The generated manifests are stored in Git and consumed by ArgoCD, which deploys them into EKS clusters.

Helm has helped us standardize deployments, reduce YAML duplication, and simplify environment management."

---

# 23. One Page Cheat Sheet

Chart = Application Package

↓

values.yaml = Variables

↓

templates = Kubernetes Manifests

↓

_helpers.tpl = Reusable Functions

↓

dependencies = Child Charts

↓

helm install = Create Release

↓

helm upgrade = Update Release

↓

helm history = View Revisions

↓

helm rollback = Restore Revision

↓

helm uninstall = Delete Release

Remember:

Helm is NOT a deployment tool.

Helm is a Kubernetes Package Manager.

ArgoCD = Deployment Orchestrator

Helm = Packaging Engine

Git = Source Of Truth

Kubernetes = Runtime Environment
