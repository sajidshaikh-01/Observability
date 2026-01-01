# PromQL – Selectors, Matchers & Modifiers
---
## Why These Concepts Matter

PromQL queries look simple, but their **power comes from**:

* Selecting the right metrics
* Filtering them correctly
* Controlling how time and labels behave

That is done using:

* **Selectors**
* **Matchers**
* **Modifiers**

---

## 1️⃣ Selectors in PromQL

### What is a Selector?

A **selector** is used to **select time-series data** from Prometheus.

In simple words:

> **Selectors decide WHICH metrics Prometheus should return.**

---

## Types of Selectors

### A) Instant Vector Selector

Selects the **latest value** of a metric.

Example:

```text
node_memory_usage_bytes
```

Meaning:

* Give me the current memory usage for all nodes

---

### B) Range Vector Selector

Selects metric values over a **time range**.

Example:

```text
node_cpu_seconds_total[5m]
```

Meaning:

* Give me CPU data for the last 5 minutes

📌 Range vectors are required for functions like `rate()`.

---

### C) Subquery Selector (Advanced)

Used to run a query over time.

Example:

```text
avg_over_time(rate(http_requests_total[1m])[10m:1m])
```

Used mainly in advanced dashboards.

---

## 2️⃣ Matchers in PromQL

### What is a Matcher?

Matchers are used **inside selectors** to **filter metrics using labels**.

In simple words:

> **Matchers decide WHICH label values are allowed.**

---

## Types of Matchers

### 1. Equality Matcher `=`

Exact match.

Example:

```text
http_requests_total{method="GET"}
```

Meaning:

* Only GET requests

---

### 2. Inequality Matcher `!=`

Exclude a value.

Example:

```text
http_requests_total{status!="200"}
```

Meaning:

* All responses except 200

---

### 3. Regex Match `=~`

Matches using regular expressions.

Example:

```text
http_requests_total{status=~"5.."}
```

Meaning:

* All 5xx errors

---

### 4. Negative Regex Match `!~`

Excludes regex matches.

Example:

```text
http_requests_total{endpoint!~"/health|/metrics"}
```

Meaning:

* Ignore health and metrics endpoints

---

## Matcher Summary Table

| Matcher | Meaning       | Example           |
| ------- | ------------- | ----------------- |
| `=`     | Equals        | `method="GET"`    |
| `!=`    | Not equals    | `status!="200"`   |
| `=~`    | Regex match   | `status=~"5.."`   |
| `!~`    | Regex exclude | `path!~"/health"` |

---

## 3️⃣ Modifiers in PromQL

### What is a Modifier?

Modifiers **change how PromQL evaluates data**, especially for:

* Time alignment
* Label matching
* Vector operations

In simple words:

> **Modifiers control HOW queries are executed.**

---

## Types of Modifiers

### A) Time Modifiers

#### `offset`

Used to query **past data**.

Example:

```text
node_cpu_usage offset 5m
```

Meaning:

* CPU usage 5 minutes ago

📌 Used for comparison (before vs now).

---

### B) Aggregation Modifiers

#### `by`

Groups results by labels.

Example:

```text
sum(rate(http_requests_total[5m])) by (service)
```

Meaning:

* Request rate per service

---

#### `without`

Drops labels while aggregating.

Example:

```text
sum(rate(http_requests_total[5m])) without (instance)
```

Meaning:

* Aggregate across instances

---

### C) Vector Matching Modifiers (VERY IMPORTANT)

Used when **combining two metrics**.

---

#### `on()`

Match only specific labels.

Example:

```text
node_cpu_usage * on(instance) node_memory_usage
```

Meaning:

* Match CPU and memory using instance label

---

#### `ignoring()`

Ignore certain labels during matching.

Example:

```text
node_cpu_usage * ignoring(cpu) node_memory_usage
```

---

#### `group_left`

Used when one metric has **more labels** than the other.

Example:

```text
node_cpu_usage * on(instance) group_left(node) node_metadata
```

---

#### `group_right`

Opposite of `group_left` (rarely used).

---

## Real Production Examples

### Example 1: Error Rate (5xx)

```text
rate(http_requests_total{status=~"5.."}[5m])
```

---

### Example 2: CPU Usage per Node

```text
sum(rate(node_cpu_seconds_total[5m])) by (instance)
```

---

### Example 3: Compare Current vs Past

```text
rate(http_requests_total[5m]) - rate(http_requests_total[5m] offset 1h)
```

---

## Common Interview Mistakes

❌ Forgetting label matchers
❌ Using `=` instead of `=~`
❌ Forgetting `on()` in vector matching
❌ Misusing `group_left`

---

## Interview One-Liners

**Selector:**

> A selector chooses which time-series data to query.

**Matcher:**

> A matcher filters metrics based on label values.

**Modifier:**

> A modifier changes how PromQL evaluates or matches data.

---
