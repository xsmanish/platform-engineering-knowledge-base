# ArgoCD Deep Dive

# Senior Platform Engineer Interview Handbook

---

# Chapter Objective

By the end of this chapter, you should be able to answer:

1. Why do we need ArgoCD?

2. Explain ArgoCD architecture.

3. Explain App of Apps.

4. Explain Drift Detection.

5. Explain Self Heal.

6. Explain Multi Cluster GitOps.

7. How do you troubleshoot ArgoCD?

8. How would you design ArgoCD at enterprise scale?

9. How is ArgoCD used in production?

10. How does this relate to my current experience?

---

# 1. Executive Summary (30 sec answer)

ArgoCD is a Kubernetes-native Continuous Delivery tool that implements GitOps principles.

Git becomes the single source of truth.

ArgoCD continuously compares the Kubernetes cluster state against Git and automatically synchronizes any differences.

Its primary responsibilities are:

* Deployment orchestration
* Drift detection
* Self-healing
* Application lifecycle management
* GitOps governance

---

# 2. The Problem ArgoCD Solves

Traditional deployment model:

Developer

↓

CI Pipeline

↓

kubectl apply

↓

Kubernetes

Problems:

* No continuous reconciliation
* Manual cluster changes
* Configuration drift
* Difficult rollbacks
* Difficult auditing
* No single source of truth

---

GitOps model:

Developer

↓

Git Repository

↓

ArgoCD

↓

Kubernetes

Benefits:

* Git is the source of truth
* Declarative deployments
* Automatic reconciliation
* Easy rollbacks
* Auditability

---

# 3. Core GitOps Principles

## Principle 1

Git is the single source of truth.

---

## Principle 2

Everything is declarative.

Examples:

* Deployments
* Services
* ConfigMaps
* Secrets
* Ingresses

---

## Principle 3

Changes happen through Git.

Never directly via kubectl.

---

## Principle 4

The system continuously reconciles state.

---

# 4. ArgoCD Architecture

High Level Architecture

```
                      Git Repository
                             |
                             |
                +-------------------------+
                | Repository Server       |
                |------------------------ |
                | Clone Repository        |
                | Render Manifests        |
                | Helm                    |
                | Kustomize               |
                +-------------------------+
                              |
                              |
```

+--------------------------------------------------------+

| API Server                                                 |
| ---------------------------------------------------------- |
| UI                                                         |
| CLI                                                        |
| Authentication                                             |
| RBAC                                                       |
| Webhooks                                                   |
| +--------------------------------------------------------+ |

```
                              |
                              |
```

+--------------------------------------------------------+

| Application Controller                                     |
| ---------------------------------------------------------- |
| Drift Detection                                            |
| Synchronization                                            |
| Health Checks                                              |
| Application Lifecycle                                      |
| +--------------------------------------------------------+ |

```
                              |
                              |
                    +----------------+
                    | Redis Cache    |
                    +----------------+
                              |
                              |
                  +----------------------+
                  | Kubernetes Cluster   |
                  +----------------------+
```

---

# 5. ArgoCD Components

## API Server

Purpose:

Acts as the entry point.

Responsibilities:

* UI
* CLI
* Authentication
* Authorization
* Webhooks

Interview keyword:

Control Plane.

---

## Repository Server

Purpose:

Clones Git repositories.

Responsibilities:

* Fetch manifests
* Render templates

Supports:

* Helm
* Kustomize
* YAML
* Jsonnet

Interview keyword:

Manifest Generation Engine

---

## Application Controller

Purpose:

Reconciliation Engine.

Responsibilities:

* Compare desired vs actual state
* Detect drift
* Trigger synchronization
* Health checks

Interview keyword:

Brain of ArgoCD

---

## Redis

Purpose:

Cache layer.

Responsibilities:

* Improve performance
* Store state temporarily

Interview keyword:

Performance Optimization

---

# 6. Application Resource

An Application is ArgoCD's fundamental object.

It defines:

Source:

* Git Repository

Destination:

* Cluster

Path:

* Application folder

Sync Policy:

* Manual
* Automated

---

Example:

Application

Source:

github.com/platform/app

↓

Destination:

eks-cluster

↓

Namespace:

payments

---

# 7. End-To-End Workflow

Developer

↓

Git Commit

↓

Repository Server clones repository

↓

Manifest generation

↓

Application Controller

↓

Desired State Comparison

↓

Drift Detection

↓

Synchronization

↓

Kubernetes

↓

Healthy Application

---

# 8. Desired State vs Actual State

Desired State

Git Repository

↓

Actual State

Kubernetes Cluster

↓

ArgoCD continuously compares both.

If different:

OutOfSync

↓

Sync

↓

Healthy

---

# 9. Drift Detection

Drift = Any manual change in Kubernetes.

Example:

Engineer runs:

kubectl scale deployment api --replicas=10

Git says:

replicas=3

ArgoCD detects mismatch.

Result:

OutOfSync

---

# 10. Self Heal

Purpose:

Automatically restore desired state.

Without Self Heal:

Manual changes persist.

With Self Heal:

ArgoCD reverts changes.

Example:

replicas=10

↓

ArgoCD

↓

replicas=3

---

# 11. Prune

Purpose:

Delete obsolete resources.

Example:

Resource removed from Git.

ArgoCD deletes it from Kubernetes.

---

# 12. Sync Policies

Manual

Requires approval.

---

Automatic

Deploy automatically.

---

Self Heal

Restore manual changes.

---

Prune

Delete unused resources.

---

# 13. App Of Apps Pattern

Enterprise Architecture

Parent Application

├── cert-manager

├── ingress

├── monitoring

├── fluentbit

├── istio

├── external-secrets

└── application-a

Benefits:

* Platform standardization
* Easier onboarding
* Environment consistency

Interview keyword:

Platform Bootstrap Pattern

---

# 14. Multi Cluster GitOps

Git

↓

Central ArgoCD

↓

Cluster A

↓

Cluster B

↓

Cluster C

Benefits:

* Centralized governance
* Single pane of glass
* Consistent deployments

---

# 15. Health Status

Healthy

↓

Progressing

↓

Suspended

↓

Missing

↓

Degraded

Know these terms.

Interviewers ask this frequently.

---

# 16. Production Usage

Typical workloads:

Platform Add-ons

├── Cert Manager

├── Istio

├── Dynatrace

├── Fluent Bit

├── EBS CSI Driver

├── Monitoring Stack

├── External Secrets

└── Application Workloads

---

# 17. Troubleshooting Tree

Application OutOfSync

↓

Can Repo Server access Git?

↓

YES

↓

Can manifests render?

↓

YES

↓

Can Controller access cluster?

↓

YES

↓

Health Check

↓

Healthy

---

# 18. Troubleshooting Commands

Applications

kubectl get applications -A

---

App Projects

kubectl get appprojects

---

Pods

kubectl get pods -n argocd

---

Services

kubectl get svc -n argocd

---

Repo Server Logs

kubectl logs deployment/argocd-repo-server -n argocd

---

Application Controller Logs

kubectl logs deployment/argocd-application-controller -n argocd

---

Application Details

kubectl describe application <app-name>

---

# 19. Reverse Engineer Your Existing Cluster

Questions to answer.

1. Is App Of Apps used?

2. Is Auto Sync enabled?

3. Is Self Heal enabled?

4. Is Prune enabled?

5. Is Multi Cluster configured?

6. How many Git repositories are connected?

7. Which platform add-ons are managed?

---

# 20. How This Connects To My Experience

NextGen Platform

↓

GitHub

↓

CodePipeline

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

"In our platform, ArgoCD acts as the deployment orchestration layer. It continuously reconciles Helm-based application manifests from Git and deploys them into EKS clusters while also managing platform add-ons."

---

# 21. Architect Level Interview Questions

Q1

Explain ArgoCD architecture.

---

Q2

Difference between CI/CD and GitOps?

---

Q3

How does ArgoCD detect drift?

---

Q4

What is App Of Apps?

---

Q5

How does Self Heal work?

---

Q6

How would you design ArgoCD for multiple clusters?

---

Q7

How do you onboard a new application?

---

Q8

How do you prevent teams from deploying directly into Kubernetes?

---

Q9

How do you troubleshoot OutOfSync?

---

Q10

How does ArgoCD integrate with Helm?

---

# 22. 5 Minute Interview Story

Question:

Explain ArgoCD in your environment.

Answer:

"In our environment, we use ArgoCD as the GitOps engine for managing both platform components and application workloads.

Applications are packaged as Helm charts and stored in Git repositories. ArgoCD continuously monitors these repositories, renders the manifests, compares them against the current cluster state, and synchronizes any differences.

We use Git as the single source of truth and avoid manual changes directly in Kubernetes.

At a platform level, ArgoCD manages add-ons such as monitoring, ingress, and certificate management. The biggest advantage has been consistency, auditability, and faster application onboarding."

---

# 23. One Page Cheat Sheet

Git = Source Of Truth

↓

Repo Server = Manifest Generator

↓

Application Controller = Reconciliation Engine

↓

Drift Detection = Detect Changes

↓

Self Heal = Revert Manual Changes

↓

Prune = Remove Old Resources

↓

App Of Apps = Bootstrap Pattern

↓

Multi Cluster = Centralized Governance

↓

Healthy Application

Remember:

GitOps is NOT a deployment tool.

GitOps is an operating model.
