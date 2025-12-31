# Prometheus Configuration File – `prometheus.yml`
---
## What is Prometheus Configuration File?

The **Prometheus configuration file** is usually named:

```text
prometheus.yml
```

It tells Prometheus:

* **WHAT to monitor**
* **WHERE to scrape metrics from**
* **HOW often to scrape**
* **HOW to discover targets**
* **WHERE to send alerts**

👉 Without this file, Prometheus does nothing.

---

## What Prometheus Learns From This File

From `prometheus.yml`, Prometheus learns:

* Scrape interval (global or per job)
* Which targets expose `/metrics`
* How service discovery works (Kubernetes, static, etc.)
* Alertmanager location
* Rule files location

---

## High-Level Structure of `prometheus.yml`

```yaml
global:
alerting:
rule_files:
scrape_configs:
```

Each section has a **specific responsibility**.

---

## 1️⃣ `global` Section

### Purpose

Defines **default settings** for Prometheus.

### Example

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s
```

### Explanation

* `scrape_interval` → how often metrics are collected
* `evaluation_interval` → how often rules are evaluated

👉 These values apply to **all jobs unless overridden**.

---

## 2️⃣ `alerting` Section

### Purpose

Tells Prometheus **where Alertmanager is running**.

### Example

```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - "alertmanager:9093"
```

### Explanation

* Prometheus sends alerts to Alertmanager
* Prometheus never sends Slack/Email directly

---

## 3️⃣ `rule_files` Section

### Purpose

Loads **alerting rules** and **recording rules**.

### Example

```yaml
rule_files:
  - "rules/*.yml"
```

### Explanation

* Rules are evaluated inside Prometheus
* Alert rules → trigger alerts
* Recording rules → precompute metrics

---

## 4️⃣ `scrape_configs` Section (MOST IMPORTANT)

### Purpose

Defines **what Prometheus scrapes**.

Each entry under `scrape_configs` is called a **job**.

---

## Example 1: Scraping Prometheus Itself

```yaml
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
```

### Explanation

* Prometheus scrapes its own metrics
* Static target (fixed address)

---

## Example 2: Scraping Node Exporter (Static)

```yaml
scrape_configs:
  - job_name: "node-exporter"
    static_configs:
      - targets:
          - "10.0.1.10:9100"
          - "10.0.1.11:9100"
```

### Explanation

* Not recommended for Kubernetes
* IPs change → breaks monitoring

---

## Example 3: Kubernetes Service Discovery (Production)

```yaml
scrape_configs:
  - job_name: "kubernetes-pods"
    kubernetes_sd_configs:
      - role: pod

    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true

      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
        action: replace
        target_label: __metrics_path__
        regex: (.+)

      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_port]
        action: replace
        target_label: __address__
        regex: (\d+)
        replacement: $1
```

### Explanation (Simple)

* Discovers all pods
* Scrapes only pods with annotation `prometheus.io/scrape=true`
* Dynamically finds IPs and ports

👉 This is **real production behavior**.

---

## Example 4: Scraping Node Exporter in Kubernetes

```yaml
scrape_configs:
  - job_name: "kubernetes-nodes"
    kubernetes_sd_configs:
      - role: node
```

### Explanation

* Automatically discovers all nodes
* Scrapes node-level metrics

---

## Important Concepts You Must Remember

### Job

* A logical group of targets
* Example: all node exporters

### Target

* A single endpoint exposing `/metrics`

### Labels

* Metadata added to metrics
* Used heavily in PromQL

---

## What Happens After Configuration is Loaded?

1. Prometheus reads `prometheus.yml`
2. Service discovery finds targets
3. Prometheus starts scraping
4. Metrics stored in TSDB
5. Rules evaluated
6. Alerts sent to Alertmanager

---

## Interview One-Liner

> The Prometheus configuration file defines global scrape settings, service discovery, scrape targets, rule files, and alertmanager endpoints, enabling Prometheus to collect and evaluate metrics dynamically.

---
