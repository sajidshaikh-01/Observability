# Prometheus Service Discovery & Relabeling – 

These two topics are **critical** because:

* Kubernetes environments are dynamic
* Pods, nodes, IPs change frequently
* Prometheus must adapt automatically

---

## 1️⃣ What is Service Discovery?

### Definition

Service Discovery is how **Prometheus automatically finds targets to scrape**.

In simple words:

> **Service Discovery tells Prometheus WHERE `/metrics` endpoints exist.**

Without service discovery:

* You must hardcode IPs
* Monitoring breaks when pods restart

---

## Why Service Discovery is Mandatory in Production

In production Kubernetes:

* Pods are created and destroyed
* IPs are ephemeral
* Autoscaling happens frequently

So Prometheus must:

* Continuously watch the environment
* Update targets dynamically

---

## Types of Service Discovery in Prometheus

### Common Production Options

| Service Discovery       | Used For                   |
| ----------------------- | -------------------------- |
| `kubernetes_sd_configs` | Kubernetes clusters        |
| `consul_sd_configs`     | VM / service registry      |
| `file_sd_configs`       | Legacy / static systems    |
| `static_configs`        | Small / non-dynamic setups |

📌 **Kubernetes Service Discovery is the most important**.

---

## Kubernetes Service Discovery (How It Really Works)

Prometheus talks to the **Kubernetes API Server**.

It discovers resources based on `role`:

| Role        | Discovers         |
| ----------- | ----------------- |
| `node`      | Kubernetes nodes  |
| `pod`       | Pods              |
| `service`   | Services          |
| `endpoints` | Service endpoints |

Example:

```yaml
kubernetes_sd_configs:
  - role: pod
```

👉 This alone discovers **ALL pods** in the cluster.

---

## Problem: Too Many Targets!

If Prometheus scrapes everything:

* System overload
* High cardinality
* Unnecessary metrics

So we need **FILTERING**.

That is where **Relabeling** comes in.

---

## 2️⃣ What is Relabeling?
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bc7ec36d-ae17-4bc1-850a-f624cab4099b" />


### Definition

Relabeling is the process of **filtering, modifying, or enriching targets and labels before scraping**.

In simple words:

> **Relabeling decides WHICH discovered targets are actually scraped and HOW they are labeled.**

---

## Two Types of Relabeling (VERY IMPORTANT)

### 1. Target Relabeling (`relabel_configs`)

* Happens **before scraping**
* Controls:

  * Which targets are kept or dropped
  * Target address & labels

### 2. Metric Relabeling (`metric_relabel_configs`)

* Happens **after scraping**
* Controls:

  * Which metrics are kept or dropped

📌 **Most production usage is target relabeling**.

---

## How Target Relabeling Works (Step-by-Step)

When Prometheus discovers a pod:

1. Kubernetes provides metadata
2. Prometheus converts metadata to labels
3. Relabel rules are applied
4. Final target list is created

---

## Kubernetes Metadata Labels (Auto-Generated)

Examples:

* `__meta_kubernetes_pod_name`
* `__meta_kubernetes_namespace`
* `__meta_kubernetes_pod_annotation_prometheus_io_scrape`
* `__meta_kubernetes_pod_label_app`

These labels exist **only during discovery**.

---

## Production Example: Scrape Only Annotated Pods

```yaml
scrape_configs:
  - job_name: "kubernetes-pods"
    kubernetes_sd_configs:
      - role: pod

    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
```

### What This Does

* Discovers all pods
* Scrapes ONLY pods with:

  ```yaml
  prometheus.io/scrape: "true"
  ```

---

## Production Example: Set Metrics Path Dynamically

```yaml
- source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
  action: replace
  target_label: __metrics_path__
  regex: (.+)
```

Meaning:

* Use custom `/metrics` path if defined in annotation

---

## Production Example: Set Target Address (Port)

```yaml
- source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_port]
  action: replace
  target_label: __address__
  regex: (\d+)
  replacement: $1
```

Meaning:

* Dynamically select the correct port

---

## Common Relabel Actions

| Action      | Purpose               |
| ----------- | --------------------- |
| `keep`      | Keep matching targets |
| `drop`      | Drop matching targets |
| `replace`   | Modify label values   |
| `labelmap`  | Copy labels           |
| `labeldrop` | Remove labels         |

---

## Metric Relabeling Example (Drop High Cardinality Metrics)

```yaml
metric_relabel_configs:
  - source_labels: [__name__]
    regex: ".*_bucket"
    action: drop
```

Used to:

* Reduce storage
* Improve performance

---

## Full Production Flow (IMPORTANT)

```text
Kubernetes API
   ↓
Service Discovery (find all targets)
   ↓
Target Relabeling (filter & modify)
   ↓
Scraping (/metrics)
   ↓
Metric Relabeling (drop unwanted metrics)
   ↓
Store in TSDB
```

---

## Common Interview Traps

❌ Thinking relabeling changes stored metrics (target relabeling does not)
❌ Scraping all pods blindly
❌ Confusing target vs metric relabeling

---

## Interview One-Liners

**Service Discovery:**

> Service discovery allows Prometheus to dynamically find scrape targets in changing environments.

**Relabeling:**

> Relabeling filters and modifies discovered targets and labels before metrics are scraped.

---

