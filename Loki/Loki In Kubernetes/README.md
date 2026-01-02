# Loki in Kubernetes –
---
## Why Loki is a Good Fit for Kubernetes

Kubernetes is:

* Dynamic (pods come and go)
* Label-driven (namespace, pod, app)

Loki is designed for this model because:

* It uses labels instead of indexing full log content
* It integrates natively with Kubernetes metadata
* It scales horizontally
* It is cost‑efficient

---

## Kubernetes Logging Basics

In Kubernetes:

* Applications write logs to **stdout / stderr**
* Container runtime stores logs as files on the node
* Log agents read these files and ship logs

Loki does **not** run inside application pods.
Instead, it uses **log agents** like Promtail.

---

## Loki Components in Kubernetes

### 1. Promtail (DaemonSet)

Runs on:

* Every Kubernetes node

Responsibilities:

* Reads container logs from node filesystem
* Discovers pods using Kubernetes API
* Attaches Kubernetes labels:

  * namespace
  * pod
  * container
  * app
* Pushes logs to Loki

Why DaemonSet:

* One Promtail per node
* Collects logs from all pods on that node

---

### 2. Loki (Stateful / Stateless Services)

Loki runs as multiple Kubernetes services depending on deployment mode.

Typical components:

* Distributor
* Ingester
* Querier
* Query Frontend
* Compactor

All components run as **Deployments** (or StatefulSets if needed).

---

### 3. Object Storage (Outside Cluster)

Loki does not store logs permanently inside Kubernetes.

Instead, it uses:

* Amazon S3
* GCS
* Azure Blob

Why external storage:

* Pods are ephemeral
* Storage must be durable
* Cost efficiency

---

## Loki Deployment Modes in Kubernetes

### 1. Single Binary (Dev / Local)

* One pod runs all Loki components
* Not recommended for production

---

### 2. Simple Scalable Mode (Most Common)

Deployed as:

* Write path:

  * Distributor (Deployment)
  * Ingester (Deployment)
* Read path:

  * Query Frontend (Deployment)
  * Querier (Deployment)
* Backend:

  * Compactor (Deployment)

Advantages:

* Easy to manage
* Independent scaling
* Used by most teams

---

### 3. Microservices Mode (Large Scale)

Each Loki component runs as a separate service:

* High operational overhead
* Used by very large platforms

---

## Log Flow in Kubernetes (End‑to‑End)

1. Pod writes logs to stdout
2. Container runtime writes log file on node
3. Promtail discovers pod via Kubernetes API
4. Promtail reads log file
5. Promtail adds labels (namespace, pod, app)
6. Promtail pushes logs to Loki Distributor
7. Loki Ingester buffers logs in memory
8. Logs flushed as chunks to object storage
9. Grafana queries Loki
10. Loki returns logs

---

## Label Strategy in Kubernetes (Very Important)

### Good Labels

* namespace
* app
* pod
* cluster
* environment

### Bad Labels (Avoid)

* request_id
* user_id
* session_id

Reason:

* High‑cardinality labels break performance

---

## Scaling Loki in Kubernetes

### Scale Write Path

* Increase distributor replicas
* Increase ingester replicas

### Scale Read Path

* Increase querier replicas
* Increase query frontend replicas

### Storage Scaling

* Object storage scales automatically

---

## High Availability in Kubernetes

* Multiple replicas of each component
* Replication factor for ingesters
* Pod disruption budgets
* Horizontal Pod Autoscaler

---

## Security in Kubernetes

### Network Security

* Use NetworkPolicies
* Restrict access to Loki

### Authentication

* Use reverse proxy or gateway
* Enable multi‑tenancy

### TLS

* Encrypt traffic between components

---

## Retention and Cost Control

* Use compactor for retention
* Apply object storage lifecycle rules
* Control log volume using relabeling

---

## Production Best Practices

* Always use object storage
* Limit label cardinality
* Separate tenants by namespace or team
* Monitor Loki with Prometheus
* Set query limits

---


