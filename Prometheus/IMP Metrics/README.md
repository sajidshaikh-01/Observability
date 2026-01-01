# High-Impact Metrics to Scrape in Prometheus 
---

## How Interviewers Think (Very Important)

Interviewers don’t care if you scraped **1000 metrics**.
They care if you can answer:

* *Why did you scrape this metric?*
* *What problem does it solve?*
* *What action do you take when it breaks?*

This guide is built exactly for that.

---

## 1️⃣ Golden Signals (🔥 MUST – Interview Favorite)

These come from Google SRE and are **extremely impressive** when mentioned.

### The 4 Golden Signals

| Signal     | What It Tells            |
| ---------- | ------------------------ |
| Latency    | Is the system slow?      |
| Traffic    | How much load is coming? |
| Errors     | Is it failing?           |
| Saturation | Are we near limits?      |

### Example Metrics

* `http_request_duration_seconds_bucket` (Latency)
* `http_requests_total` (Traffic)
* `http_requests_total{status=~"5.."}` (Errors)
* `container_cpu_usage_seconds_total` (Saturation)

👉 **Interview line:**

> "I primarily monitor the Golden Signals because they directly reflect user experience."

---

## 2️⃣ Node-Level Metrics (Infrastructure Health)

### Why These Matter

If nodes are unhealthy → **everything breaks**.

### Important Metrics

* `node_cpu_seconds_total`
* `node_memory_MemAvailable_bytes`
* `node_disk_io_time_seconds_total`
* `node_filesystem_avail_bytes`
* `node_network_receive_bytes_total`

### Interview Use Case

> "If latency increases, first I check node CPU, memory pressure, and disk IO."

---

## 3️⃣ Kubernetes Cluster Metrics (Very Important)

Scraped via **kube-state-metrics**.

### High-Value Metrics

* `kube_node_status_condition`
* `kube_pod_status_phase`
* `kube_pod_container_status_restarts_total`
* `kube_deployment_status_replicas_available`
* `kube_node_status_allocatable_cpu_cores`

### Why Interviewers Like This

Shows you understand **desired state vs actual state**.

---

## 4️⃣ Pod & Container Metrics (Resource Efficiency)

### Key Metrics

* `container_cpu_usage_seconds_total`
* `container_memory_working_set_bytes`
* `container_cpu_cfs_throttled_seconds_total`
* `container_oom_events_total`

### Strong Interview Insight

> "CPU throttling is often more dangerous than high CPU usage."

---

## 5️⃣ Application-Level Metrics (🔥 Differentiator)

This is where **most candidates fail**.

### What You Should Instrument

* Request latency
* Error rate
* Throughput
* Dependency failures

### Example Metrics

* `http_server_requests_seconds_bucket`
* `app_errors_total`
* `db_query_duration_seconds`
* `external_api_failures_total`

### Interview Line (Powerful)

> "Infra metrics tell me *something is wrong*, app metrics tell me *what is wrong*."

---

## 6️⃣ SLO / SLA Metrics (Advanced but Impressive)

### Example SLO Metrics

* Availability %
* Error budget burn rate

### Example PromQL

```
1 - (sum(rate(http_requests_total{status=~"5.."}[5m]))
     / sum(rate(http_requests_total[5m])))
```

### Interview Impact

Very few 1 YOE candidates talk about SLOs → **huge plus**.

---

## 7️⃣ Alert-Worthy Metrics (Actionable Only)

### Good Alert Metrics

* High error rate
* High latency (p95 / p99)
* Node disk full
* Pod crash loops

### Bad Alert Metrics

* CPU > 80% always
* Memory usage alone

👉 **Interview Tip:**

> "I alert only on symptoms, not raw metrics."

---

## 8️⃣ Autoscaling Metrics (HPA / KEDA – Very Useful)

### Metrics Used for Scaling

* CPU utilization
* Memory utilization
* Request rate
* Queue length

### Example

* `requests_per_second`
* `kafka_consumer_lag`

### Interview Line

> "We use Prometheus metrics to drive autoscaling decisions using HPA or KEDA."

---

## 9️⃣ What NOT to Scrape (Shows Maturity)

Avoid:

* High-cardinality labels (`user_id`, `request_id`)
* Metrics with no action
* Debug-only metrics in prod

### Interview Quote

> "If a metric doesn’t drive a decision, it doesn’t belong in production."

---

## 🔥 Interview-Ready Summary (Say This)

> "In Prometheus, I focus on scraping metrics that represent user experience, system health, and scaling signals — mainly Golden Signals, node and pod health, application latency, and error rates. I avoid scraping metrics that don’t lead to action."

---

## What Makes YOU Look Different

✔ Golden Signals
✔ Application metrics
✔ SLO thinking
✔ Actionable alerts
✔ Autoscaling metrics

This makes the interviewer think:

> **"This guy understands production, not just Prometheus."**

---

