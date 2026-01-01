# Prometheus Long-Term Storage – 

---

## Why Long-Term Storage is Needed in Prometheus

By default, Prometheus:

* Stores metrics **locally on disk**
* Uses its internal **TSDB (Time Series Database)**

This is good for:

* Short-term monitoring
* Fast queries

But it is **not enough for production**.

---

## Limitations of Default Prometheus Storage

Prometheus TSDB has these limitations:

1. **Single-node storage**

   * Data is stored only on the Prometheus pod

2. **Limited retention**

   * Usually 7–15 days
   * Increasing retention increases disk & memory usage

3. **No global view**

   * Cannot easily query metrics across multiple clusters

4. **No built-in high availability**

   * If Prometheus pod is lost, data may be lost

Because of this:

> **Prometheus alone is not suitable for long-term metrics retention.**

---

## What is Long-Term Storage?

Long-term storage means:

* Storing metrics for **months or years**
* Storing metrics **outside Prometheus**
* Enabling **global queries**
* Supporting **high availability**

---

## How Prometheus Achieves Long-Term Storage

Prometheus uses an **external system** for long-term storage.

Prometheus:

* Still scrapes metrics
* Still evaluates rules
* **Ships metrics to remote storage**

This is done using:

* `remote_write`
* `remote_read`

---

## Common Long-Term Storage Solutions

### 1️⃣ Thanos (MOST COMMON)

Used when:

* Kubernetes clusters
* Need HA Prometheus
* Need S3-based storage

Key features:

* Uses object storage (S3, GCS, Azure Blob)
* Enables querying across clusters
* Supports HA Prometheus

---

### 2️⃣ Cortex / Mimir

Used when:

* Very large scale
* Multi-tenant environments
* SaaS monitoring platforms

Key features:

* Horizontally scalable
* Multi-tenant
* Very high ingestion rate

---

### 3️⃣ VictoriaMetrics

Used when:

* Simpler setup is needed
* High performance is required

Key features:

* Single binary option
* Low resource usage

---

## Thanos Architecture (Production Standard)

In production, Thanos is the **most commonly used** solution.

Components:

### Thanos Sidecar

* Runs alongside Prometheus
* Uploads TSDB blocks to object storage

### Object Storage

* S3 / GCS / Azure Blob
* Stores metrics for long term

### Thanos Query

* Provides a global query layer
* Combines data from multiple Prometheus instances

### Thanos Store Gateway

* Reads historical data from object storage

---

## How Data Flows (Thanos Example)

1. Prometheus scrapes metrics
2. Metrics stored locally in TSDB
3. Thanos sidecar uploads blocks to S3
4. Store Gateway reads data from S3
5. Thanos Query serves global queries

Prometheus still handles **real-time data**.

---

## Remote Write (Alternative Method)

Prometheus can push metrics directly to remote storage.

Example use cases:

* Cortex
* Mimir
* VictoriaMetrics

Prometheus configuration includes:

* `remote_write`

This allows:

* Near real-time ingestion
* External long-term retention

---

## Retention Strategy (Production)

Typical production setup:

* Prometheus local retention: 7–15 days
* Long-term storage retention: 6 months – 2 years

Reason:

* Fast queries for recent data
* Cheap storage for historical data

---

## Why We Still Keep Local Prometheus Storage

Even with long-term storage:

* Prometheus is faster for recent data
* Alerts depend on local data
* Reduces load on remote storage

Long-term storage is **not a replacement**, it is an extension.

---

## Multi-Cluster Monitoring

Long-term storage enables:

* Central monitoring for many clusters
* Global dashboards
* Cross-cluster SLOs

Without long-term storage:

* Each cluster is isolated

---

## Common Interview Traps

❌ "Prometheus itself stores data long-term" → Wrong
❌ "Increasing retention is enough" → Wrong
❌ "Thanos replaces Prometheus" → Wrong

---

## Interview One-Liner

> Prometheus uses external systems like Thanos or Cortex for long-term storage by offloading metrics to object storage, enabling long retention, high availability, and global querying while keeping Prometheus focused on real-time monitoring.

---

