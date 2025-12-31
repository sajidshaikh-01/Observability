# Metrics in Prometheus – Types

This is written for:

* Kubernetes & cloud monitoring
* Production understanding
* DevOps / SRE interviews

---

## What is a Metric?

A **metric** is a **numeric measurement collected over time** that represents the behavior or health of a system.

In simple words:

> Metrics tell us **how a system is behaving**, not what exactly happened.

---

## Examples of Metrics

* CPU usage
* Memory usage
* Number of requests
* Request latency
* Error rate

Metrics are stored as **time-series data**:

```text
<metric_name>{label="value"} number timestamp
```

---

## Why Metrics Are Important

Metrics are used for:

* Monitoring system health
* Alerting
* Autoscaling (HPA)
* SLI / SLO calculations
* Capacity planning

Logs tell **what happened**.
Metrics tell **how the system behaves over time**.

---

## Metric Structure in Prometheus

Example:

```text
http_requests_total{method="GET",status="200"} 1200
```

Explanation:

* `http_requests_total` → metric name
* `method`, `status` → labels
* `1200` → value

---

## Prometheus Metric Types

Prometheus supports **4 main metric types**:

1. Counter
2. Gauge
3. Histogram
4. Summary

---

## 1️⃣ Counter

### What is a Counter?

A **counter** is a metric that:

* Only **increases**
* Can reset to zero (on restart)

---

### When to Use Counter

* Number of requests
* Number of errors
* Number of jobs completed

---

### Counter Example

```text
http_requests_total 10500
```

To calculate rate:

```text
rate(http_requests_total[5m])
```

📌 **Counters are always used with `rate()` in PromQL**.

---

## 2️⃣ Gauge

### What is a Gauge?

A **gauge** represents a value that:

* Can **increase or decrease**
* Represents current state

---

### When to Use Gauge

* CPU usage
* Memory usage
* Number of running pods

---

### Gauge Example

```text
node_memory_usage_bytes 524288000
```

Gauge values change freely.

---

## 3️⃣ Histogram

### What is a Histogram?

A **histogram** measures the **distribution of values over time**.

Used mostly for:

* Latency
* Request duration
* Response size

---

### Histogram Components

A histogram creates:

* `_bucket`
* `_sum`
* `_count`

Example:

```text
http_request_duration_seconds_bucket
http_request_duration_seconds_sum
http_request_duration_seconds_count
```

---

### Histogram Use Case

Used to answer:

> "How many requests took less than 500ms?"

Latency calculation example:

```text
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

---

## 4️⃣ Summary

### What is a Summary?

A **summary** is similar to histogram but:

* Calculates quantiles on the **client side**
* More expensive

---

### Summary Characteristics

* `_sum`
* `_count`
* Quantiles (0.9, 0.95, etc.)

Example:

```text
http_request_duration_seconds{quantile="0.95"}
```

---

### Histogram vs Summary (Important)

| Aspect               | Histogram   | Summary     |
| -------------------- | ----------- | ----------- |
| Quantile calculation | Server-side | Client-side |
| Aggregation          | Yes         | No          |
| Performance          | Better      | Costly      |
| Production use       | Preferred   | Rare        |

📌 **Most production systems prefer Histogram**.

---

## Which Metric Type to Use? (Quick Guide)

| Use Case       | Metric Type |
| -------------- | ----------- |
| Total requests | Counter     |
| CPU / Memory   | Gauge       |
| Latency        | Histogram   |
| Percentiles    | Histogram   |

---

## Metrics in Kubernetes (Real Examples)

* `container_cpu_usage_seconds_total` → Counter
* `container_memory_usage_bytes` → Gauge
* `apiserver_request_duration_seconds` → Histogram

---

## Common Mistakes (Interview Trap)

❌ Using Gauge for request count
❌ Using Counter without `rate()`
❌ Using Summary for cluster-wide latency

---

## Interview One-Liners

**Metric:**

> A numeric time-series measurement that represents system behavior over time.

**Counter:**

> A monotonically increasing metric used for counting events.

**Gauge:**

> A metric that represents a value that can go up and down.

**Histogram:**

> A metric that samples observations into buckets to measure distributions.

**Summary:**

> A metric that calculates quantiles on the client side.

---

