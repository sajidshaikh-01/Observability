
# PromQL – Prometheus Query Language 
---
## What is PromQL?

PromQL (Prometheus Query Language) is the **query language used by Prometheus** to:

* Read metrics from the time-series database (TSDB)
* Aggregate metrics
* Calculate rates and averages
* Build dashboards
* Define alerting rules

In simple words:

> **PromQL is how we ask questions to Prometheus metrics.**

---

## Why PromQL is Important

Prometheus only **stores raw metrics**.

PromQL is required to:

* Convert raw metrics into meaningful values
* Detect problems
* Calculate SLIs / SLOs
* Trigger alerts

Without PromQL → metrics are useless numbers.

---

## Where PromQL is Used

PromQL is used in:

* Prometheus Web UI
* Grafana dashboards
* Alerting rules
* Recording rules
* Autoscaling logic (custom metrics)

---

## How PromQL Works (Mental Model)

1. Prometheus stores metrics as time-series
2. PromQL selects one or more time-series
3. PromQL applies functions and operators
4. Result is returned as instant or range vector

---

## Core Data Types in PromQL

### 1️⃣ Instant Vector

* Set of time-series with **one value per series**

Example:

```text
node_cpu_seconds_total
```

---

### 2️⃣ Range Vector

* Set of time-series over a **time range**

Example:

```text
node_cpu_seconds_total[5m]
```

---

### 3️⃣ Scalar

* Single numeric value

Example:

```text
1
```

---

### 4️⃣ String (rarely used)

---

## Basic PromQL Syntax

### Metric Selector

```text
metric_name
```

Example:

```text
container_memory_usage_bytes
```

---

### Label Selector

```text
metric_name{label="value"}
```

Example:

```text
http_requests_total{status="500"}
```

---

### Multiple Labels

```text
http_requests_total{method="GET",status="200"}
```

---

## Time Range Selector

```text
metric_name[5m]
```

Used only with **range vectors**.

---

## Functions (Very Important)

### rate() – MOST IMPORTANT

Used for **counters**.

```text
rate(http_requests_total[5m])
```

---

### increase()

Total increase over time range.

```text
increase(http_requests_total[1h])
```

---

### avg()

Average value.

```text
avg(container_memory_usage_bytes)
```

---

### sum()

Sum of values.

```text
sum(rate(http_requests_total[5m]))
```

---

### min() / max()

```text
max(node_cpu_usage)
```

---

## Aggregation Operators

### Grouping with `by`

```text
sum(rate(http_requests_total[5m])) by (service)
```

---

### Excluding labels with `without`

```text
sum(rate(http_requests_total[5m])) without (instance)
```

---

## Arithmetic Operations

```text
(node_memory_usage_bytes / node_memory_total_bytes) * 100
```

Used for percentage calculations.

---

## Comparison Operators

```text
node_cpu_usage > 80
```

Used mainly in **alerting rules**.

---

## Logical Operators

```text
(node_cpu_usage > 80) and (node_memory_usage > 70)
```

---

## Histogram Queries (Advanced but Important)

### Latency Calculation

```text
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

Used for:

* P95 latency
* SLO calculations

---

## PromQL in Alerting Rules

Example:

```yaml
expr: rate(http_requests_total{status="500"}[5m]) > 1
```

---

## PromQL in Recording Rules

Example:

```yaml
expr: sum(rate(http_requests_total[5m])) by (service)
record: service:http_requests:rate5m
```

---

## Common PromQL Mistakes (Interview Traps)

❌ Using counter without `rate()`
❌ Forgetting time range `[5m]`
❌ Using Summary for aggregation
❌ Too many labels causing high cardinality

---

## Interview One-Liners

**PromQL:**

> PromQL is the query language used by Prometheus to select, aggregate, and calculate metrics stored as time-series data.

**rate():**

> rate() calculates per-second average increase of a counter over a time range.

---

