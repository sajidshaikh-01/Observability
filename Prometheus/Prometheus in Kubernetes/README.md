# Prometheus in Kubernetes – How It Runs in Production

This is written for:

* Real-world Kubernetes operations
* DevOps / SRE interviews
* Clear mental model (no confusion)

---

## High-Level Idea (One Line)

> In Kubernetes, Prometheus runs as a **centralized monitoring service** that dynamically discovers cluster components, scrapes their metrics, stores them as time-series data, and triggers alerts via Alertmanager.

---

## Why Prometheus is Needed in Kubernetes

Kubernetes is:

* Dynamic (pods scale up/down)
* Ephemeral (pods restart, IPs change)
* Distributed (many nodes and services)

Traditional monitoring fails here.

Prometheus works because it:

* Uses **service discovery**
* Uses **labels instead of fixed hosts**
* Is **cloud-native by design**

---

## How Prometheus is Deployed in Kubernetes

### 1️⃣ Prometheus Runs as a Pod

In production, Prometheus runs as:

* A **Deployment** or **StatefulSet**
* Usually **1 replica** (small/medium clusters)
* **2 replicas** for HA (large clusters)

Important:

* Prometheus is **NOT** a DaemonSet
* It does **NOT** run on every node

Kubernetes schedules the Prometheus pod on **any available node**.

---

## Core Components Deployed in Kubernetes

A typical production setup deploys:

### 1. Prometheus Server

* Central metrics scraper
* Stores metrics in local TSDB
* Evaluates alert & recording rules

---

### 2. Node Exporter (DaemonSet)

* Runs on **every node**
* Exposes OS-level metrics
* Example metrics:

  * CPU
  * Memory
  * Disk

---

### 3. kube-state-metrics (Deployment)

* Exposes Kubernetes object state
* Examples:

  * Pod status
  * Deployment replicas
  * Node conditions

Important:

* Does NOT expose CPU/memory

---

### 4. cAdvisor (Kubelet-integrated)

* Exposes container-level metrics
* Used for pod & container resource usage

---

### 5. Alertmanager

* Runs as a separate Deployment
* Handles alert routing, grouping, silencing

---

### 6. Grafana (Optional but Common)

* Visualizes metrics
* Queries Prometheus using PromQL

---

## How Prometheus Discovers Targets in Kubernetes

Prometheus uses **Kubernetes Service Discovery**.

It talks to:

* Kubernetes API Server

It discovers:

* Nodes
* Pods
* Services
* Endpoints

This is done using:

* `kubernetes_sd_configs`

---

## Target Selection (Very Important)

Prometheus initially discovers **everything**.

In production, we **filter targets** using:

* Target relabeling

Example logic:

* Scrape only pods with specific annotations
* Ignore system or test namespaces

This prevents overload.

---

## Scraping Metrics (How Data is Collected)

1. Targets expose `/metrics`
2. Prometheus pulls metrics at `scrape_interval`
3. Metrics are collected over HTTP/HTTPS

Important:

* Prometheus always scrapes **all metrics exposed** by a target

---

## Storage in Kubernetes (TSDB)

Prometheus stores data:

* Locally on disk
* As time-series blocks

In Kubernetes:

* Uses **PersistentVolume (PV/PVC)**

Without PVC:

* Metrics are lost on pod restart

---

## Alerting in Kubernetes

### Alert Evaluation

* Prometheus evaluates alert rules internally
* Uses PromQL expressions

### Alert Delivery

* Alerts are sent to Alertmanager
* Alertmanager routes alerts to:

  * Slack
  * Email
  * PagerDuty

Prometheus never sends notifications directly.

---

## Security in Production Kubernetes

Prometheus uses:

* Kubernetes ServiceAccount
* RBAC permissions

RBAC controls:

* Which resources Prometheus can read

TLS is used for:

* Secure scraping
* Secure API communication

---

## High Availability (HA) Setup

For large clusters:

* Run **2 Prometheus replicas**
* Both scrape same targets
* Alertmanager deduplicates alerts

For long-term storage:

* Thanos or Cortex is added

---

## Common Production Installation Method

Most companies use:

* **kube-prometheus-stack (Helm)**

It installs:

* Prometheus
* Alertmanager
* Grafana
* Exporters
* Prebuilt dashboards

---

## Complete Production Workflow

```text
Nodes / Pods / Apps
   ↓
Exporters expose /metrics
   ↓
Prometheus discovers targets via K8s API
   ↓
Target relabeling filters targets
   ↓
Prometheus scrapes metrics
   ↓
Metrics stored in TSDB (PVC)
   ↓
Alert rules evaluated
   ↓
Alerts sent to Alertmanager
   ↓
Notifications sent
   ↓
Grafana visualizes metrics
```

---

## What Prometheus Does NOT Do

❌ Does not collect logs
❌ Does not trace requests
❌ Does not auto-scale itself

Prometheus is **metrics-only**.

---

## Common Interview Traps

❌ "Prometheus runs on every node" → Wrong
❌ "Prometheus pushes metrics" → Wrong
❌ "kube-state-metrics gives CPU" → Wrong

---

## Interview One-Liner

> In Kubernetes, Prometheus runs as a centralized service that uses Kubernetes service discovery to scrape metrics from nodes, pods, and applications, stores them as time-series data, and integrates with Alertmanager and Grafana for alerting and visualization.

---

