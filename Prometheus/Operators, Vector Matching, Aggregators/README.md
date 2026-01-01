# PromQL – Operators, Vector Matching & Aggregators 

These topics are **very important for interviews and real incident debugging**.

---

## 1️⃣ Operators in PromQL

### What are Operators?

Operators are used to **perform calculations or comparisons** on metrics.

PromQL operators work on:

* Scalars
* Instant vectors
* Range vectors (with functions)

---

## Types of Operators

### A) Arithmetic Operators

Used to perform mathematical calculations.

| Operator | Meaning        |
| -------- | -------------- |
| `+`      | Addition       |
| `-`      | Subtraction    |
| `*`      | Multiplication |
| `/`      | Division       |
| `%`      | Modulo         |
| `^`      | Power          |

#### Example: CPU Usage Percentage

```text
(node_memory_usage_bytes / node_memory_total_bytes) * 100
```

---

### B) Comparison Operators

Used mostly in **alerting rules**.

| Operator | Meaning          |
| -------- | ---------------- |
| `==`     | Equal            |
| `!=`     | Not equal        |
| `>`      | Greater than     |
| `<`      | Less than        |
| `>=`     | Greater or equal |
| `<=`     | Less or equal    |

#### Example: High CPU Alert Condition

```text
node_cpu_usage > 80
```

Returns only time-series where condition is true.

---

### C) Logical Operators

Used to combine conditions.

| Operator | Meaning                     |
| -------- | --------------------------- |
| `and`    | Both conditions true        |
| `or`     | Either condition true       |
| `unless` | Left side except right side |

#### Example: CPU high AND memory high

```text
(node_cpu_usage > 80) and (node_memory_usage > 75)
```

---

## 2️⃣ Vector Matching in PromQL

### What is Vector Matching?

Vector matching defines **how two metrics are matched together using labels** when applying operators.

In simple words:

> Vector matching decides **which time-series from left and right should be combined**.

---

## Why Vector Matching is Needed

Example problem:

* CPU metric has labels: `instance`, `cpu`
* Memory metric has labels: `instance`

Prometheus needs rules to match them correctly.

---

## Types of Vector Matching

### A) One-to-One Matching (Default)

Both metrics must have **exact same labels**.

Example:

```text
node_cpu_usage * node_memory_usage
```

Works only if label sets match exactly.

---

### B) `on()` Modifier

Match using **only specific labels**.

```text
node_cpu_usage * on(instance) node_memory_usage
```

Meaning:

* Match CPU and memory using `instance` label only

---

### C) `ignoring()` Modifier

Ignore certain labels while matching.

```text
node_cpu_usage * ignoring(cpu) node_memory_usage
```

Meaning:

* Ignore `cpu` label during matching

---

### D) Many-to-One Matching (`group_left`)

Used when **right side has fewer labels**.

```text
node_cpu_usage * on(instance) group_left node_metadata
```

Example use case:

* Join CPU metrics with node metadata

---

### E) One-to-Many Matching (`group_right`)

Opposite of `group_left` (rare in production).

---

## 3️⃣ Aggregators in PromQL

### What are Aggregators?

Aggregators **combine multiple time-series into fewer time-series**.

In simple words:

> Aggregators summarize data.

---

## Common Aggregation Functions

| Aggregator | Purpose            |
| ---------- | ------------------ |
| `sum`      | Total              |
| `avg`      | Average            |
| `min`      | Minimum            |
| `max`      | Maximum            |
| `count`    | Count series       |
| `stddev`   | Standard deviation |
| `stdvar`   | Variance           |
| `topk`     | Top N values       |
| `bottomk`  | Bottom N values    |

---

## Aggregation Syntax

### Grouping with `by`

```text
sum(rate(http_requests_total[5m])) by (service)
```

Meaning:

* Request rate per service

---

### Dropping Labels with `without`

```text
sum(rate(http_requests_total[5m])) without (instance)
```

Meaning:

* Aggregate across instances

---

## Real Production Examples

### Example 1: Total Request Rate

```text
sum(rate(http_requests_total[5m]))
```

---

### Example 2: Error Rate per Service

```text
sum(rate(http_requests_total{status=~"5.."}[5m])) by (service)
```

---

### Example 3: Top 5 Pods by Memory

```text
topk(5, container_memory_usage_bytes)
```

---

## Common Interview Mistakes

❌ Forgetting vector matching rules
❌ Misusing `group_left`
❌ Aggregating before applying `rate()`
❌ Wrong label grouping

---

## Interview One-Liners

**Operators:**

> Operators perform arithmetic, comparison, or logical operations on metrics.

**Vector Matching:**

> Vector matching defines how PromQL matches time-series using labels when combining metrics.

**Aggregators:**

> Aggregators summarize multiple time-series into fewer series using functions like sum or avg.

---

