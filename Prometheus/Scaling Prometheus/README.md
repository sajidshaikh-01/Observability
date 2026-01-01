# Scaling Prometheus –

## Why Scaling Prometheus Is Required

Prometheus is designed as a **single-node time-series database (TSDB)**. While it works very well for small and medium environments, production systems introduce challenges such as:

* Thousands of scrape targets
* High metric cardinality
* Long-term retention requirements
* Multiple Kubernetes clusters

### Core Limitation

* One Prometheus = one TSDB
* CPU, memory, and disk are hard limits

👉 Prometheus **does not scale by adding replicas** like normal applications. It scales using **architecture patterns**.

---

## Prometheus Scaling Approaches (High Level)

1. Vertical Scaling
2. Horizontal Scaling (Sharding)
3. Federation
4. High Availability (HA)
5. Global Scaling using Thanos

---

## 1️⃣ Vertical Scaling

### What It Means

* Increase CPU, memory, and disk of a single Prometheus instance

### When Used

* Small to medium clusters
* Low scrape load
* Low metric cardinality

### Pros

* Very simple
* No architectural changes

### Cons

* Hard upper limit
* Not suitable for large-scale production

**Interview Note:**

> Vertical scaling is usually the first step, not the final solution.

---

## 2️⃣ Horizontal Scaling via Sharding

### What Is Sharding?

* Split scraping responsibility across **multiple Prometheus servers**
* Each Prometheus scrapes **only a subset of targets**

### Example

* Prometheus-A → Node & system metrics
* Prometheus-B → Application metrics

Sharding can be based on:

* Namespace
* Application
* Team
* Environment

### Pros

* Reduces load per Prometheus
* Better performance and stability

### Cons

* No single global query view
* Dashboards become complex

**Interview Note:**

> Prometheus does not automatically shard; it must be architected manually.

---

## 3️⃣ Federation (Native Prometheus Scaling)

### What Is Federation?

* One Prometheus scrapes metrics from other Prometheus servers

### Types of Federation

#### Hierarchical Federation

* Leaf Prometheus → Scrapes workloads
* Global Prometheus → Scrapes leaf Prometheus

#### Cross Federation

* Prometheus servers scrape each other

### Use Cases

* Large environments
* Regional clusters

### Limitations

* No long-term storage
* No true high availability
* Top-level Prometheus still has scaling limits

**Interview Question:**
Why federation is not enough for modern production?

**Answer:**

* No durable storage
* Limited HA
* Query performance issues

---

## 4️⃣ High Availability (HA) Scaling

### What Is HA in Prometheus?

* Run **two or more Prometheus replicas** scraping the same targets

### Why HA Is Needed

* Avoid data loss during restart or failure
* Improve reliability

### How It Works

* All replicas scrape the same endpoints
* Each stores data independently

### Problem

* Duplicate metrics

### Solution

* Deduplication using external labels
* Tools like Thanos Query handle deduplication

**Important:**

> HA improves reliability, NOT capacity.

---

## 5️⃣ Global Scaling with Thanos (Production Standard)

### Why Thanos Is Used

* Prometheus cannot scale globally on its own

### What Thanos Adds

* Object storage (S3 / GCS / Azure Blob)
* Global query layer
* Deduplication for HA Prometheus
* Downsampling for cost savings

### Common Production Pattern

* Multiple Prometheus (per cluster or per team)
* Thanos Sidecar attached to each Prometheus
* Central Thanos Query
* Grafana queries Thanos Query

---

## Cardinality – The Real Scaling Killer

### What Is Cardinality?

* Number of unique label combinations

### Why It Matters

* High cardinality increases memory usage
* Can crash Prometheus even with low scrape count

### Bad Label Examples

* user_id
* request_id
* session_id

### Good Label Examples

* app
* namespace
* environment

### Cardinality Control Techniques

* Metric relabeling
* Drop unused labels
* Avoid dynamic labels

**Interviewers LOVE this topic.**

---

## Scaling Prometheus in Kubernetes / EKS

### Best Practices

* Use Prometheus Operator
* Separate Prometheus for:

  * Platform metrics
  * Application metrics
* Apply resource limits
* Control scrape intervals

### Typical Production Setup

* Prometheus (per cluster)
* Thanos Sidecar
* Central Thanos Query
* Grafana on top

---

## What NOT to Do

* One Prometheus scraping everything
* Very long retention on local disk
* Ignoring metric cardinality
* Running Prometheus without HA

---

## Interview One-Liners (Memorize)

* "Prometheus scales via sharding and federation, not by replicas."
* "High availability prevents data loss but does not increase capacity."
* "Thanos is the industry-standard solution for global Prometheus scaling."

---

## Expected Knowledge for 1 Year Experience

You SHOULD know:

* Why Prometheus scaling is difficult
* Sharding vs Federation
* HA concept
* Why Thanos is needed

You are NOT expected to know:

* TSDB internals
* WAL tuning
* Advanced compactor optimizations

---

