<img width="1416" height="790" alt="image" src="https://github.com/user-attachments/assets/dca2b725-bffe-4c5b-97df-accdbf07ee46" />


# Alertmanager – How It Works in Production 
---
## What is Alertmanager?

Alertmanager is a **separate component** in the Prometheus ecosystem that is responsible for:

* Receiving alerts from Prometheus
* Grouping related alerts
* Deduplicating duplicate alerts
* Silencing alerts
* Routing alerts to correct receivers (Slack, Email, PagerDuty, etc.)

> **Prometheus detects problems. Alertmanager decides who to notify and how.**

---

## Why Alertmanager Is Needed (Production Reality)

Without Alertmanager:

* Every alert fires independently
* Engineers get flooded with notifications
* Same alert repeats every minute

Alertmanager exists to **reduce noise and control alert delivery**.

---

## High-Level Production Flow

```text
Metrics → PromQL → Alert Rule → Prometheus → Alertmanager → Notification
```

---

## Step-by-Step: How Alertmanager Works in Production

---

## Step 1️⃣ Metric Collection

* Applications and exporters expose `/metrics`
* Prometheus scrapes metrics at defined intervals
* Metrics are stored in TSDB

At this stage, **Alertmanager is not involved**.

---

## Step 2️⃣ Alert Rule Evaluation (Inside Prometheus)

Prometheus evaluates **alerting rules** at every `evaluation_interval`.

Example logic:

* CPU usage > 80%
* Condition must hold for 5 minutes

If condition matches:

* Alert state becomes **FIRING**

---

## Step 3️⃣ Prometheus Sends Alert to Alertmanager

Once an alert is FIRING:

* Prometheus sends alert data to Alertmanager over HTTP
* Alert includes:

  * Alert name
  * Labels (severity, service, instance)
  * Annotations (summary, description)

Important:

> Prometheus does **NOT** send notifications directly.

---

## Step 4️⃣ Alertmanager Receives Alerts

Alertmanager receives alerts and temporarily stores them in memory.

At this point, Alertmanager:

* Does NOT notify immediately
* First processes alerts using its configuration

---

## Step 5️⃣ Grouping Alerts (Noise Reduction)

Alertmanager groups alerts based on labels.

Example:

* 10 pods of same service crash
* Instead of 10 messages
* One grouped alert is sent

Grouping is controlled by:

* `group_by`
* `group_wait`
* `group_interval`

---

## Step 6️⃣ Deduplication

Alertmanager checks:

* Has this alert already been sent?

If yes:

* It does NOT resend the same notification repeatedly

This prevents alert spam.

---

## Step 7️⃣ Routing Alerts

Alertmanager decides **where to send the alert** based on labels.

Routing logic examples:

* `severity=critical` → PagerDuty
* `severity=warning` → Slack
* `team=payments` → Payments team channel

Routing is hierarchical and rule-based.

---

## Step 8️⃣ Apply Silences (If Any)

Before sending notifications, Alertmanager checks silences.

Silences are used when:

* Maintenance window
* Planned deployments
* Known issues

If a silence matches:

* Alert is suppressed
* No notification is sent

---

## Step 9️⃣ Send Notification to Receiver

After grouping, deduplication, routing, and silence checks:

* Alertmanager sends notification to receivers

Common receivers:

* Slack
* Email
* PagerDuty
* OpsGenie
* Webhooks

---

## Step 🔟 Alert Resolution Handling

When the problem is fixed:

* Prometheus marks alert as **RESOLVED**
* Alertmanager sends a **resolved notification** (if enabled)

This closes the incident.

---

## Full Production Workflow (One View)

```text
1. Metrics scraped by Prometheus
2. Alert rules evaluated
3. Alert fires (FIRING)
4. Prometheus sends alert to Alertmanager
5. Alertmanager groups alerts
6. Deduplicates alerts
7. Applies routing rules
8. Applies silences
9. Sends notification
10. Sends RESOLVED notification
```

---

## What Alertmanager Does NOT Do

❌ Does not scrape metrics
❌ Does not evaluate PromQL
❌ Does not detect problems
❌ Does not store metrics

Alertmanager is **notification logic only**.

---

## Production Best Practices

* Always define `severity` labels
* Use grouping to reduce noise
* Separate critical and non-critical alerts
* Use silences during maintenance
* Test alerts before production

---

## Common Interview Traps

❌ "Alertmanager detects alerts" → Wrong
❌ "Prometheus sends Slack alerts" → Wrong
❌ "Alertmanager stores metrics" → Wrong

---

## Interview One-Liner (Very Important)

> Alertmanager receives alerts from Prometheus, groups and deduplicates them, applies routing and silencing rules, and sends notifications to the appropriate receivers.

---

