# Helm Hands-On Workshop - Deploy PostgreSQL To EKS

# Senior Platform Engineer Interview Handbook

---

# Chapter Objective

By the end of this chapter, I should be able to:

1. Create a Helm chart from scratch.

2. Understand Helm chart structure.

3. Build a PostgreSQL chart.

4. Customize values.

5. Deploy PostgreSQL to EKS.

6. Upgrade PostgreSQL.

7. Rollback PostgreSQL.

8. Delete PostgreSQL.

9. Integrate Helm with ArgoCD.

10. Explain this in an interview.

---

# 1. What Are We Building?

We will build this architecture.

```
                PostgreSQL Helm Chart

                            |

                            |

                +-----------------------+

                | values.yaml           |

                | deployment.yaml       |

                | service.yaml          |

                | pvc.yaml              |

                | secret.yaml           |

                +-----------------------+

                            |

                            |

                Kubernetes API Server

                            |

                            |

                  EKS Kubernetes Cluster

                            |

            +---------------------------+

            | PostgreSQL Pod            |

            | PostgreSQL Service        |

            | Persistent Volume Claim   |

            | Secret                    |

            +---------------------------+
```

---

# 2. Prerequisites

You need:

kubectl

helm

AWS CLI

Configured kubeconfig

Verify:

kubectl get nodes

helm version

kubectl config current-context

---

# 3. Step 1 - Create Namespace

kubectl create namespace postgres

Verify:

kubectl get ns

---

# 4. Step 2 - Generate Helm Chart

Run:

helm create postgresql

Helm creates:

postgresql/

Chart.yaml

values.yaml

charts/

templates/

_helpers.tpl

tests/

Go inside:

cd postgresql

---

# 5. Understanding The Folder Structure

postgresql/

├── Chart.yaml

├── values.yaml

├── templates/

│   ├── deployment.yaml

│   ├── service.yaml

│   ├── pvc.yaml

│   ├── secret.yaml

│   └── _helpers.tpl

└── charts/

Purpose:

Chart.yaml

↓

Metadata

values.yaml

↓

Variables

templates/

↓

Kubernetes resources

_helpers.tpl

↓

Reusable functions

charts/

↓

Dependencies

---

# 6. Step 3 - Edit Chart.yaml

Replace with:

apiVersion: v2

name: postgresql

description: PostgreSQL Helm Chart

type: application

version: 1.0.0

appVersion: "16"

---

# 7. Step 4 - Create values.yaml

postgres:

image:

repository: postgres

tag: "16"

replicas: 1

database:

name: mydb

user: admin

password: mypassword

storage:

size: 10Gi

service:

type: ClusterIP

port: 5432

---

# 8. Step 5 - Create Secret

templates/secret.yaml

apiVersion: v1

kind: Secret

metadata:

name: postgres-secret

type: Opaque

stringData:

POSTGRES_DB: {{ .Values.database.name }}

POSTGRES_USER: {{ .Values.database.user }}

POSTGRES_PASSWORD: {{ .Values.database.password }}

---

# 9. Step 6 - Create PVC

templates/pvc.yaml

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

name: postgres-pvc

spec:

accessModes:

* ReadWriteOnce

resources:

requests:

storage: {{ .Values.storage.size }}

---

# 10. Step 7 - Create Deployment

templates/deployment.yaml

apiVersion: apps/v1

kind: Deployment

metadata:

name: postgres

spec:

replicas: {{ .Values.postgres.replicas }}

selector:

matchLabels:

app: postgres

template:

metadata:

labels:

app: postgres

spec:

containers:

* name: postgres

image: "{{ .Values.postgres.image.repository }}:{{ .Values.postgres.image.tag }}"

ports:

* containerPort: 5432

envFrom:

* secretRef:

name: postgres-secret

volumeMounts:

* mountPath: /var/lib/postgresql/data

name: postgres-storage

volumes:

* name: postgres-storage

persistentVolumeClaim:

claimName: postgres-pvc

---

# 11. Step 8 - Create Service

templates/service.yaml

apiVersion: v1

kind: Service

metadata:

name: postgres

spec:

type: {{ .Values.service.type }}

ports:

* port: {{ .Values.service.port }}

targetPort: 5432

selector:

app: postgres

---

# 12. Step 9 - Validate Chart

helm lint .

Expected:

0 Errors

---

# 13. Step 10 - Render Manifests

helm template .

This does NOT deploy.

It only renders YAML.

Very important interview point.

---

# 14. Step 11 - Install Chart

helm install postgres . -n postgres

Verify:

helm list -n postgres

kubectl get pods -n postgres

kubectl get svc -n postgres

kubectl get pvc -n postgres

---

# 15. Step 12 - Connect To PostgreSQL

kubectl exec -it deployment/postgres -n postgres -- bash

psql

-U admin

-d mydb

---

# 16. Step 13 - Upgrade Deployment

Change:

values.yaml

storage:

size: 20Gi

Then:

helm upgrade postgres . -n postgres

Verify:

helm history postgres -n postgres

---

# 17. Step 14 - Rollback

helm rollback postgres 1 -n postgres

Verify:

helm history postgres -n postgres

---

# 18. Step 15 - Uninstall

helm uninstall postgres -n postgres

Delete namespace:

kubectl delete ns postgres

---

# 19. How ArgoCD Uses Helm

GitHub Repository

↓

Helm Chart

↓

ArgoCD Repo Server

↓

Manifest Generation

↓

Application Controller

↓

EKS

↓

PostgreSQL Running

Important:

ArgoCD does NOT run Helm CLI.

It renders Helm templates internally.

Interviewers ask this frequently.

---

# 20. Production Deployment Architecture

GitHub

↓

CodePipeline

↓

Docker Build

↓

ECR

↓

Helm Chart

↓

ArgoCD

↓

EKS

↓

Application

This is how our environment works.

---

# 21. Interview Questions

Q1

How do you create a Helm chart?

---

Q2

What does helm create do?

---

Q3

What does helm template do?

---

Q4

What does helm lint do?

---

Q5

How does ArgoCD use Helm?

---

Q6

How do you rollback a deployment?

---

Q7

How do you separate environments?

Answer:

values-dev.yaml

values-qa.yaml

values-prod.yaml

---

Q8

How do you upgrade PostgreSQL?

Answer:

Modify values.yaml

↓

helm upgrade

---

# 22. 5 Minute Interview Story

Question:

Explain how you would deploy PostgreSQL to EKS using Helm.

Answer:

"I would first generate a Helm chart using helm create. Then I would customize Chart.yaml, values.yaml, Deployment, Service, PVC, and Secret templates.

Once validated using helm lint and helm template, I would deploy the chart using helm install.

For upgrades, I would modify values.yaml and use helm upgrade. If something failed, I would use helm rollback to restore the previous release."

---

# 23. One Page Cheat Sheet

helm create

↓

Edit values.yaml

↓

Create templates

↓

helm lint

↓

helm template

↓

helm install

↓

helm upgrade

↓

helm history

↓

helm rollback

↓

helm uninstall

Remember:

helm template = Render YAML

helm install = Deploy

helm upgrade = Update

helm rollback = Restore

ArgoCD = Orchestrator

Helm = Packaging Engine

Kubernetes = Runtime

# 24. Interview Questions & Answers

## Q1. How do you create a Helm chart?

### 2 Minute Answer

"I would start by generating a chart skeleton using `helm create`. This creates Chart.yaml, values.yaml, templates, and helper files.

Then I would customize the chart by updating the application metadata, adding environment-specific values, and creating templates such as Deployment, Service, Secret, and PersistentVolumeClaim.

I would validate the chart using `helm lint` and `helm template`, and finally deploy it using `helm install`."

---

## Q2. What does `helm create` do?

### 2 Minute Answer

"`helm create` generates a boilerplate Helm chart structure.

It creates:

* Chart.yaml
* values.yaml
* templates/
* _helpers.tpl
* tests/

This provides a starting point so we don't have to build a chart from scratch."

---

## Q3. What does `helm template` do?

### 2 Minute Answer

"`helm template` only renders Kubernetes manifests locally.

It does NOT deploy anything.

It is used to validate how templates and values will be converted into final Kubernetes YAML before deployment."

Example:

helm template .

This is extremely useful for debugging."

---

## Q4. What does `helm lint` do?

### 2 Minute Answer

"`helm lint` validates the chart structure and checks for syntax issues.

It catches problems such as:

* Missing values
* Invalid templates
* YAML syntax errors
* Chart configuration issues

We should always run `helm lint` before deployment."

---

## Q5. How does ArgoCD use Helm?

### 2 Minute Answer

"ArgoCD does not execute Helm CLI commands.

Instead, ArgoCD internally renders Helm charts using its Repository Server.

The rendered manifests are then passed to the Application Controller, which synchronizes them to Kubernetes.

The flow is:

Git Repository

↓

ArgoCD Repo Server

↓

Helm Rendering

↓

Manifest Generation

↓

Application Controller

↓

EKS Cluster"

---

## Q6. How do you rollback a deployment?

### 2 Minute Answer

"Helm stores release history for every deployment.

If a deployment fails, I can view previous revisions using:

helm history <release>

Then rollback using:

helm rollback <release> <revision>

For example:

helm rollback postgres 1

This restores the application to a previously working state."

---

## Q7. How do you manage multiple environments?

### 2 Minute Answer

"I use separate values files for each environment.

For example:

values-dev.yaml

values-qa.yaml

values-uat.yaml

values-prod.yaml

The same chart is reused, and only the values change.

This avoids duplication and keeps deployments consistent."

Deployment example:

helm install postgres . -f values-prod.yaml

---

## Q8. How do you upgrade PostgreSQL?

### 2 Minute Answer

"I would modify values.yaml or the environment-specific values file and execute:

helm upgrade postgres .

Helm will compare the existing release with the new configuration and apply only the required changes."

---

# 25. Senior Architect Answers (5 Minute Answer)

## Q1. Explain Helm architecture.

"Helm is a package manager for Kubernetes that standardizes application deployments.

Applications are packaged into Helm charts containing metadata, variables, templates, and dependencies.

The deployment flow is:

Developer

↓

Helm Chart

↓

Values Files

↓

Template Rendering

↓

Kubernetes YAML

↓

Kubernetes API Server

↓

Cluster Resources

In enterprise environments, Helm primarily solves three problems:

1. YAML duplication

2. Environment management

3. Release lifecycle management

Helm itself is stateless. It renders manifests and sends them to Kubernetes.

When integrated with ArgoCD, Helm acts as the packaging engine while ArgoCD acts as the deployment orchestrator."

---

## Q2. Explain how PostgreSQL would be deployed using Helm.

"I would first generate a Helm chart using `helm create`.

Then I would customize:

Chart.yaml

↓

Metadata

values.yaml

↓

Database configuration

deployment.yaml

↓

PostgreSQL container

service.yaml

↓

ClusterIP service

secret.yaml

↓

Credentials

pvc.yaml

↓

Persistent storage

After validating the chart with `helm lint` and `helm template`, I would deploy it using `helm install`.

For upgrades, I would modify values.yaml and execute `helm upgrade`.

If an issue occurred, I would rollback using `helm rollback`.

This provides a complete lifecycle management solution."

---

## Q3. Explain Helm's role in your environment.

"In our platform, Helm acts as the application packaging layer.

Application teams create reusable Helm charts containing Deployments, Services, ConfigMaps, and Ingress resources.

These charts are stored in Git repositories and consumed by ArgoCD.

ArgoCD then deploys them into EKS clusters.

This separation of responsibilities allows:

Helm

↓

Package applications

ArgoCD

↓

Deploy applications

Git

↓

Source of truth

EKS

↓

Runtime environment

This architecture has helped standardize deployments and reduce operational complexity."

---

# 26. Interview Cheat Sheet

Question:

How do I create a chart?

Answer:

helm create

↓

Edit Chart.yaml

↓

Edit values.yaml

↓

Create templates

↓

helm lint

↓

helm template

↓

helm install

---

Question:

How do I upgrade?

Answer:

Update values

↓

helm upgrade

---

Question:

How do I rollback?

Answer:

helm history

↓

helm rollback

---

Question:

How does ArgoCD use Helm?

Answer:

Git

↓

Repo Server

↓

Helm Render

↓

Application Controller

↓

EKS

---

Question:

How do I manage environments?

Answer:

Use:

values-dev.yaml

values-qa.yaml

values-uat.yaml

values-prod.yaml

Never duplicate charts.
