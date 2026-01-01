# PromQL Functions – Complete Guide with Examples

Functions are one of the **most important parts of PromQL** and are heavily used in:

* Dashboards (Grafana)
* Alerting rules
* SLI / SLO calculations
* Incident debugging

---

## What are PromQL Functions?

PromQL functions are **built-in operations** that:

* Transform raw metrics
* Calculate rates, averages, percentiles
* Analyze behavior over time

In simple words:

> **Functions turn raw metrics into useful insights.**

---

## Categories of PromQL Functions

PromQL functions can be grouped into:

1. Rate & Counter functions
2. Aggregation over time functions
3. Mathematical & transformation functions
4. Histogram & latency functions
5. Prediction & trend functions

---

## 1️⃣ Rate & Counter Functions (MOST IMPORTANT)

These are used mainly with **Counter metrics**.

---

### `rate()` ⭐

Calculates the **per-second average rate of increase** of a counter.

```text
rate(http_requests_total[5m])
```

Use case:

* Requests per second
* Error rate

📌 **Always use `rate()` with counters.**

---

### `irate()`

Calculates the **instant rate** using the last two samples.

```text
irate(http_requests_total[1m])
```

Use case:

* Short spikes

⚠️ Not recommended for alerts.

---

### `increase()`

Calculates **total increase** over a time range.

```text
increase(http_requests_total[1h])
```

Use case:

* Total requests in last hour

---

### `resets()`

Counts how many times a counter was reset.

```text
resets(process_cpu_seconds_total[1h])
```

Use case:

* Detect application restarts

---

## 2️⃣ Aggregation Over Time Functions

These operate on **range vectors**.

---

### `avg_over_time()`

Average value over time.

```text
avg_over_time(node_memory_usage_bytes[10m])
```

---

### `min_over_time()` / `max_over_time()`

```text
max_over_time(cpu_usage[5m])
```

---

### `sum_over_time()`

Sum of values over time.

```text
sum_over_time(container_restarts_total[1h])
```

---

### `count_over_time()`

Counts samples in a range.

```text
count_over_time(http_requests_total[5m])
```

---

## 3️⃣ Mathematical & Transformation Functions

---

### `abs()`

Returns absolute value.

```text
abs(node_cpu_usage)
```

---

### `ceil()` / `floor()`

```text
ceil(node_cpu_usage)
```

---

### `round()`

Rounds values.

```text
round(node_cpu_usage, 0.01)
```

---

### `clamp_min()` / `clamp_max()`

```text
clamp_max(cpu_usage, 80)
```

Use case:

* Limit values for dashboards

---

## 4️⃣ Histogram & Latency Functions (VERY IMPORTANT)

Used with **Histogram metrics**.

---

### `histogram_quantile()` ⭐

Calculates latency percentiles.

```text
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

Use cases:

* P95 / P99 latency
* SLO monitoring

---

## 5️⃣ Prediction & Trend Functions

---

### `predict_linear()`

Predicts future value based on trend.

```text
predict_linear(node_filesystem_free_bytes[1h], 3600)
```

Use case:

* Disk will fill in 1 hour?

---

### `deriv()`

Estimates derivative (trend).

```text
deriv(cpu_usage[10m])
```

---

## 6️⃣ Time & Timestamp Functions

---

### `time()`

Returns current Unix timestamp.

```text
time()
```

---

### `timestamp()`

Returns timestamp of a metric sample.

```text
timestamp(node_cpu_usage)
```

---

## Real Production Examples

### Error Rate per Service

```text
sum(rate(http_requests_total{status=~"5.."}[5m])) by (service)
```

---

### CPU Usage Percentage

```text
rate(node_cpu_seconds_total[5m]) * 100
```

---

### P95 Latency

```text
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

---

## Common Interview Mistakes

❌ Using counters without `rate()`
❌ Forgetting range selector `[5m]`
❌ Using `irate()` for alerts
❌ Misusing histogram_quantile without buckets

---

## Interview One-Liners

**rate():**

> Calculates per-second average rate of increase of a counter.

**increase():**

> Calculates total increase over a time range.

**histogram_quantile():**

> Calculates latency percentiles from histogram buckets.

---

