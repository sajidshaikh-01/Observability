<img width="1200" height="372" alt="image" src="https://github.com/user-attachments/assets/11e56285-db9b-4686-808d-9164deaf94a8" />

# Loki Architecture – 
---
## High-Level Architecture Overview

Loki is a **horizontally scalable log aggregation system** that separates **write path** and **read path** and uses **object storage as the source of truth**.

Core design principles:

* Index only labels (not log content)
* Store logs as compressed chunks
* Use cheap, durable object storage
* Scale reads and writes independently

---

## High-Level Data Flow

1. Application writes logs to stdout/stderr
2. Container runtime stores logs on node
3. Promtail reads logs and adds labels
4. Promtail pushes logs to Loki (write path)
5. Loki stores data in object storage
6. Grafana queries Loki (read path)

---

## Loki Internal Architecture Components

### 1. Distributor (Write Path Entry)

Responsibilities:

* Entry point for incoming logs
* Validates labels and tenant ID
* Splits logs into streams
* Uses consistent hashing
* Replicates logs to multiple ingesters

Why it matters:

* Load balancing
* High availability
* Multi-tenancy support

---

### 2. Ingester (Write Path Core)

Responsibilities:

* Receives log streams from distributor
* Buffers logs in memory
* Groups logs into chunks
* Compresses chunks
* Flushes chunks to object storage

Key production concepts:

* WAL (Write-Ahead Log) for crash recovery
* Replication factor ensures durability
* Memory-heavy component

---

### 3. Object Storage (Source of Truth)

Responsibilities:

* Stores compressed log chunks
* Stores index files (boltdb-shipper)

Examples:

* Amazon S3
* Google Cloud Storage
* Azure Blob Storage

Why object storage:

* Cheap
* Durable
* Infinitely scalable

---

### 4. Index Store – boltdb-shipper

Responsibilities:

* Stores label indexes only
* No full-text indexing
* Index files stored in object storage

How it works:

* Ingesters write index files
* Queriers download index files on demand

Why boltdb-shipper:

* No external database
* Simple operations
* Cloud-native

---

### 5. Querier (Read Path Core)

Responsibilities:

* Receives queries from frontend
* Reads index to locate chunks
* Fetches chunks from object storage
* Decompresses and filters logs

Optimizations:

* Parallel chunk fetching
* Time-based query splitting

---

### 6. Query Frontend (Read Path Optimizer)

Responsibilities:

* Sits in front of queriers
* Splits large queries
* Caches query results
* Enforces query limits

Why used in production:

* Faster queries
* Protects backend from heavy queries

---

### 7. Compactor (Background Component)

Responsibilities:

* Merges small index files
* Applies retention policies
* Deletes expired data

Why important:

* Reduces storage cost
* Improves query performance

---

## Write Path Summary

Flow:

Promtail → Distributor → Ingester → Object Storage

Characteristics:

* Horizontally scalable
* Fault tolerant
* High availability

---

## Read Path Summary

Flow:

Grafana → Query Frontend → Querier → Object Storage

Characteristics:

* Read scalability
* Query caching
* Independent scaling from write path

---

## Loki Deployment Modes

### 1. Single Binary Mode

* All components run together
* Used for development / POC

### 2. Simple Scalable Mode (Most Common)

* Write: Distributor + Ingester
* Read: Query Frontend + Querier
* Backend: Compactor + Storage

### 3. Microservices Mode

* Each component deployed independently
* Used by very large-scale platforms

---

## Production Architecture Characteristics

* Stateless services (except ingester memory)
* Object storage as single source of truth
* Easy horizontal scaling
* Kubernetes-native design

---

 for **label-based indexing**, making it cheaper, simpler, and more scalable than traditional log systems.
