# Prometheus Security – Authentication & Encryption (How It Works)

We will cover:

* Why security is needed
* Authentication (who can talk)
* Authorization (what they can do)
* Encryption (how data is protected)

---

## Why Do We Need Security in Prometheus?

Prometheus handles **sensitive operational data**:

* Infrastructure metrics
* Application behavior
* Performance bottlenecks

If unsecured:

* Anyone can read metrics
* Attackers can learn system internals
* Alerts and dashboards can be abused

So we secure:

1. **Scraping (Prometheus → targets)**
2. **Query access (Users/Grafana → Prometheus)**
3. **Alerting (Prometheus → Alertmanager)**

---

## Security Layers in Prometheus

Prometheus security works at **multiple layers**:

1. Transport security (TLS / Encryption)
2. Authentication (identity verification)
3. Authorization (access control – mostly external)

---

## 1️⃣ Encryption – How Data Is Protected

### What Is Encryption?

Encryption ensures data is **not readable in transit**.

Prometheus uses **TLS (HTTPS)** for encryption.

---

### Where Encryption Is Used

| Communication             | Encryption  |
| ------------------------- | ----------- |
| Prometheus → Exporters    | TLS (HTTPS) |
| Prometheus → Alertmanager | TLS         |
| Grafana → Prometheus      | TLS         |
| User → Prometheus UI      | TLS         |

---

### How TLS Works (Simple Flow)

1. Client connects using HTTPS
2. Server presents TLS certificate
3. Certificate is validated
4. Encrypted channel is established
5. Metrics/data flow securely

---

### TLS in `prometheus.yml` (Example)

```yaml
scrape_configs:
  - job_name: "secure-node-exporter"
    scheme: https
    tls_config:
      ca_file: /etc/prometheus/certs/ca.crt
      cert_file: /etc/prometheus/certs/client.crt
      key_file: /etc/prometheus/certs/client.key
      insecure_skip_verify: false
```

### Meaning

* `scheme: https` → enable TLS
* `ca_file` → verify server certificate
* `cert_file` / `key_file` → client identity (mTLS)

---

## 2️⃣ Authentication – Who Is Allowed?

Authentication answers:

> **Who is calling me?**

Prometheus supports **multiple authentication methods**.

---

### A) Basic Authentication

Used mainly for:

* Prometheus → targets
* Prometheus → Alertmanager

Example:

```yaml
basic_auth:
  username: prometheus
  password: strongpassword
```

📌 Simple but **not recommended alone** without TLS.

---

### B) Bearer Token Authentication (Kubernetes – MOST COMMON)

In Kubernetes, Prometheus authenticates using **Service Account tokens**.

How it works:

1. Prometheus runs as a Pod
2. Pod has a ServiceAccount
3. Kubernetes mounts a token
4. Prometheus uses token to talk to Kubernetes API

Example:

```yaml
bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
```

📌 This is how Prometheus discovers pods/nodes securely.

---

### C) Mutual TLS (mTLS) – Strongest Authentication

mTLS means:

* Client proves identity
* Server proves identity

Used in **high-security environments**.

Flow:

1. Prometheus presents client cert
2. Target verifies it
3. Target presents server cert
4. Prometheus verifies it

---

## 3️⃣ Authorization – What Are You Allowed To Do?

Authorization answers:

> **What can you access or modify?**

⚠️ Prometheus has **very limited built-in authorization**.

---

### How Authorization Is Done in Production

#### A) Kubernetes RBAC (MOST IMPORTANT)

* Controls what Prometheus can see
* Applied at Kubernetes API level

Example permissions:

* Read nodes
* Read pods
* Read services

Prometheus **cannot access anything outside RBAC permissions**.

---

#### B) Reverse Proxy Authorization

Most companies put Prometheus **behind a reverse proxy** (NGINX, Envoy).

Proxy handles:

* User authentication
* Role-based access

Prometheus stays simple.

---

## 4️⃣ Securing Prometheus UI

By default:

* Prometheus UI has **NO authentication** ❌

Production solution:

* Put Prometheus behind:

  * NGINX
  * OAuth
  * SSO

This provides:

* Login
* Access control

---

## 5️⃣ Secure Alerting Flow

1. Prometheus evaluates alert
2. Sends alert over HTTPS
3. Alertmanager authenticates Prometheus
4. Alertmanager routes alert securely

---

## Complete Security Flow (Simple)

```text
TLS encrypts data
Authentication verifies identity
Authorization limits access
```

---

## Common Production Best Practices

* Always enable TLS
* Never expose Prometheus publicly
* Use Kubernetes RBAC
* Use ServiceAccount tokens
* Secure Alertmanager endpoints
* Put Prometheus behind reverse proxy

---

## Interview One-Liner

> Prometheus security relies on TLS for encryption, service account tokens or certificates for authentication, and external systems like Kubernetes RBAC or reverse proxies for authorization.

---



