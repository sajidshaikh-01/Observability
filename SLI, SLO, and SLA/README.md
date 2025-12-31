# SLI, SLO, and SLA – Complete Explanation 
## Why SLI, SLO, SLA Exist

Modern systems must be:

* Reliable
* Available
* Fast

But saying **"our system is good"** is meaningless.

So we define **measurable reliability goals** using:

* **SLI** (Service Level Indicator)
* **SLO** (Service Level Objective)
* **SLA** (Service Level Agreement)

These concepts are the **foundation of SRE and Observability**.

---

## 1. SLI (Service Level Indicator)

### What is an SLI?

An **SLI** is a **metric that measures how well a service is performing**.

In simple words:

> **SLI = What we measure**

---

### Examples of SLIs

Common SLIs in production:

* Request success rate
* Latency
* Availability
* Error rate
* Throughput

Example:

```text
SLI: Percentage of successful HTTP requests
```

---

### SLI Example (Real)

Formula:

```text
SLI = (Successful requests / Total requests) × 100
```

If:

* Total requests = 1,000,000
* Failed requests = 1,000

SLI = 99.9%

---

### Where SLIs Come From

SLIs are calculated using **metrics** from monitoring systems:

* Prometheus
* CloudWatch
* Datadog

---

## 2. SLO (Service Level Objective)

### What is an SLO?

An **SLO** is the **target value or goal** for an SLI.

In simple words:

> **SLO = How good the service should be**

---

### SLO Examples

| SLI          | SLO                  |
| ------------ | -------------------- |
| Availability | 99.9% per month      |
| Latency      | 95% requests < 500ms |
| Error Rate   | < 0.1%               |

---

### SLO Example (Real)

```text
SLI: HTTP request success rate
SLO: 99.9% success rate over 30 days
```

This means:

* Small failures are acceptable
* Perfection is NOT required

---

### Error Budget (Very Important)

Error Budget = 100% − SLO

Example:

```text
SLO = 99.9%
Error Budget = 0.1%
```

This budget:

* Allows deployments
* Allows experiments
* Controls risk

If error budget is exhausted:

* Freeze deployments
* Focus on reliability

---

## 3. SLA (Service Level Agreement)

### What is an SLA?

An **SLA** is a **formal contract with customers**.

In simple words:

> **SLA = What we legally promise customers**

---

### SLA Characteristics

* External (customer-facing)
* Legal document
* Includes penalties
* Written in business language

---

### SLA Example

```text
Service availability will be 99.5% monthly.
If availability drops below this, customers receive service credits.
```

---

### SLA vs SLO (Key Difference)

* SLA is **less strict** than SLO
* SLA protects the business
* SLO protects engineering quality

---

## Relationship Between SLI, SLO, SLA

Simple chain:

```text
Metrics → SLI → SLO → SLA
```

Explanation:

* Metrics are raw data
* SLI measures performance
* SLO sets internal targets
* SLA sets external promises

---

## SLI vs SLO vs SLA (Comparison Table)

| Aspect      | SLI         | SLO               | SLA       |
| ----------- | ----------- | ----------------- | --------- |
| Meaning     | Measurement | Target            | Contract  |
| Audience    | Engineers   | Engineering + SRE | Customers |
| Flexibility | High        | Medium            | Low       |
| Penalty     | No          | No                | Yes       |

---

## SLI/SLO/SLA in Kubernetes & Cloud (Production)

Typical examples:

* API success rate
* Pod availability
* Request latency
* Error percentage

Used for:

* Alerting thresholds
* Scaling decisions
* Release gating
* Reliability reporting

---

## Interview One-Liners

**SLI:**

> A quantitative metric that measures service performance.

**SLO:**

> A target value for an SLI that defines acceptable service reliability.

**SLA:**

> A contractual agreement that defines service guarantees and penalties.

---

#
