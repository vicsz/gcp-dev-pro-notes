# GCP Developer Pro for AWS Developers
_A focused AWS → GCP service mapping and exam heuristic guide for the Google Professional Cloud Developer exam_

This document maps AWS services to their GCP equivalents **only where relevant to the Professional Cloud Developer exam**, with explicit notes on where AWS instincts commonly lead to incorrect answers.

---

## Compute and Application Runtimes

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| EC2 | Compute Engine | Same IaaS concept. Usually a fallback or migration option, not the preferred answer. |
| ECS (Fargate) | Cloud Run | **Most important mapping**. Cloud Run is the default container runtime. No clusters, no nodes, scale-to-zero. |
| EKS | GKE | Correct only when Kubernetes features are explicitly required (custom networking, GPUs, daemonsets). |
| Lambda | Cloud Functions | Event-driven functions, but often a distractor when Cloud Run would work. |
| Elastic Beanstalk | App Engine | Legacy/simple workloads. Often appears as a wrong-but-plausible option. |

---

## Object Storage

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| S3 Standard | Cloud Storage Standard | Default object storage for apps and static assets. |
| S3 Intelligent-Tiering | Cloud Storage Autoclass | Automatic tiering based on access patterns. |
| S3 Standard-IA | Cloud Storage Nearline | Infrequent access, ~30-day minimum storage. |
| S3 Glacier | Cloud Storage Coldline | Rare access, ~90-day minimum. |
| S3 Glacier Deep Archive | Cloud Storage Archive | Very rare access, ~365-day minimum. |
| S3 Events | Cloud Storage Events | Frequently combined with Pub/Sub or Eventarc. |

**Exam tip**  
> If the question mentions *cost optimization* + *infrequent access* → Nearline or Coldline  
> If it mentions *compliance or long-term retention* → Archive

---

## File and Block Storage

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| EBS | Persistent Disk | Mostly implicit via Compute Engine or GKE. |
| EFS | Filestore | Appears mainly with GKE shared filesystem use cases. |

---
## Databases and Datastores

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| DynamoDB | Firestore | **Primary application datastore**. Document-based, strongly consistent, auto-scaling, minimal ops. |
| DynamoDB Global Tables | Firestore (multi-region) | Firestore supports multi-region replication natively. |
| Aurora / RDS | Cloud SQL | Managed relational DB, but **scaling limits and regional scope are emphasized**. |
| Aurora Global | Spanner | **Very important**. Global, strongly consistent, horizontally scalable relational DB. |
| ElastiCache | Memorystore | Redis-based caching. Simple and mostly equivalent. |
| Redshift | BigQuery | BigQuery is serverless, SQL-first, analytics-oriented. |
| Neptune | No direct equivalent | Graph use cases are rare; usually a distractor. |

---

### Cloud SQL – Supported Engines (Exam-Relevant)

Cloud SQL supports the following engines:

| Engine | Notes for the Exam |
|---|---|
| **PostgreSQL** | Most commonly referenced. Default relational choice. |
| **MySQL** | Supported and equivalent in exam context. |
| **SQL Server** | Supported, but appears mostly in migration scenarios. |

**Not supported in Cloud SQL**
- ❌ Oracle
- ❌ MariaDB (Cloud SQL used to support it; no longer emphasized)

**Exam takeaway**
> If Oracle is required → Cloud SQL is *not* the answer  
> Expect **Postgres** to be the default relational assumption

---

### Firestore (Very High Exam Weight)

**Firestore characteristics**
- Document-oriented (NoSQL)
- Strong consistency by default
- Automatic scaling
- Multi-region support
- Very low operational overhead

**Firestore vs DynamoDB (Exam framing)**
- Firestore emphasizes **simplicity and consistency**
- Fewer knobs, more opinionated
- Often the *preferred* answer unless relational constraints are explicit

**Common exam use cases**
- User profiles
- Application state
- Metadata
- Mobile and web backends

---

### Spanner (High Exam Weight)

**Spanner characteristics**
- Fully managed relational database
- Global, multi-region
- Strong consistency
- Horizontal scaling
- ANSI SQL

**Spanner is chosen when**
- Global transactions are required
- Strong consistency across regions is required
- Cloud SQL scaling is insufficient

**Exam takeaway**
> If the question says *global*, *multi-region*, *strong consistency*, or *planet-scale* → **Spanner**

---

### Cloud SQL (Medium Exam Weight)

**Cloud SQL characteristics**
- Regional
- Vertically scalable (limited horizontal options)
- Requires more operational consideration than Firestore or Spanner

**Typical exam framing**
- Legacy or migration scenarios
- Traditional relational schemas
- When Firestore is *not suitable*

**Exam trap**
> Cloud SQL is often a **plausible but suboptimal** answer

---

### BigQuery (Analytics, Not OLTP)

**BigQuery characteristics**
- Serverless
- Columnar
- SQL-based
- Optimized for analytics, not transactions

**Exam takeaway**
> If the workload is reporting, analytics, or large-scale querying → BigQuery  
> If it’s OLTP → Firestore, Cloud SQL, or Spanner

---

### Memorystore

**Memorystore characteristics**
- Managed Redis
- Used for caching, sessions, ephemeral data

**Exam framing**
- Straightforward
- Rarely tricky
- Usually paired with Cloud Run or GKE

---

## Database Selection Heuristics (Exam-Critical)

Use these rules aggressively:

- **Application data, high scale, low ops** → Firestore
- **Global relational consistency** → Spanner
- **Traditional relational, regional** → Cloud SQL (Postgres/MySQL)
- **Analytics / reporting** → BigQuery
- **Caching / sessions** → Memorystore
- **If Oracle is required** → migration scenario, not Cloud SQL

---

## Common AWS → GCP Exam Traps (With Correct Choices)

- **Defaulting to Cloud SQL when Firestore is sufficient**  
  **Why this is wrong:** Cloud SQL introduces unnecessary operational overhead and scaling limits.  
  **Correct choice:** **Firestore** for application data, user profiles, metadata, and high-scale workloads.

- **Treating Spanner as “expensive Aurora”**  
  **Why this is wrong:** Spanner is not a performance tier of Cloud SQL; it is a globally distributed, strongly consistent relational database.  
  **Correct choice:** **Spanner** when global transactions, multi-region writes, or strong consistency across regions are required.

- **Using BigQuery for transactional workloads**  
  **Why this is wrong:** BigQuery is optimized for analytics and reporting, not low-latency OLTP.  
  **Correct choice:**  
  - **Firestore** for non-relational OLTP  
  - **Cloud SQL** for regional relational OLTP  
  - **Spanner** for global relational OLTP

- **Over-valuing schema flexibility over operational simplicity**  
  **Why this is wrong:** The exam favors managed, opinionated services that reduce operational burden.  
  **Correct choice:**  
  - Prefer **Firestore** over Cloud SQL unless relational constraints are explicit  
  - Prefer **Spanner** over custom sharding or replication strategies

- **Assuming relational databases are the default**  
  **Why this is wrong:** GCP’s application-first guidance prioritizes NoSQL and globally managed services.  
  **Correct choice:** Start with **Firestore**, move to **Cloud SQL** only when required, and to **Spanner** when global scale is mandatory.

---

## Messaging and Eventing

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| SQS | Pub/Sub | Pub/Sub replaces queues and topics. Core service. |
| SNS | Pub/Sub | Fan-out is native. Push and pull both valid. |
| EventBridge | Eventarc | Routes platform and audit-log events to Cloud Run. |
| Kinesis | Dataflow | Dataflow handles streaming + batch pipelines. |

**Exam tip**  
> Any async, decoupled, or event-driven architecture → Pub/Sub

---

## APIs, Traffic, and Integration

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| API Gateway | API Gateway | OpenAPI-driven, simpler than AWS. |
| ALB / Global Accelerator | Cloud Load Balancing | Global HTTP(S) LB is default and assumed. |
| Step Functions | Workflows | YAML-based orchestration. Frequently tested. |

**Exam tip**  
> Multi-step service orchestration → Workflows, not Pub/Sub

---

## Identity and Security (Developer Scope)

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| IAM Roles | Service Accounts | **Extremely important**. Everything runs as a service account. |
| STS | Workload Identity | Replaces long-lived credentials. |
| Secrets Manager | Secret Manager | Injected into Cloud Run / Functions via env vars. |
| KMS | Cloud KMS | CMEK and envelope encryption scenarios. |

**Exam tip**  
> If credentials or keys are stored in code → wrong answer  
> Service Accounts + Workload Identity is always preferred

---

## Networking (Light but Testable)

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| VPC | VPC | GCP VPCs are **global**; subnets are regional. |
| Security Groups | Firewall Rules | Applied at VPC level, not instance level. |
| PrivateLink | Private Service Connect | Private access to managed services. |
| NAT Gateway | Cloud NAT | Mostly invisible in serverless contexts. |
| Route 53 | Cloud DNS | Straightforward DNS service. |

---

## Observability

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| CloudWatch | Cloud Monitoring | Metrics, alerts, SLOs emphasized. |
| CloudWatch Logs | Cloud Logging | **Structured logging (JSON) is important**. |
| X-Ray | Cloud Trace | Appears conceptually (latency, tracing). |

**Exam tip**  
> Logs are expected to be structured and queryable

---

## CI/CD and Build

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| CodeBuild | Cloud Build | Central CI service. Common in questions. |
| CodePipeline | Cloud Build + Deploy | Composable pipeline model. |
| ECR | Artifact Registry | Stores containers and language artifacts. |

---

## Analytics and Data (Developer-Relevant)

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| Athena | BigQuery | **Important**. Serverless, SQL-first analytics. |
| Glue | Dataflow | Managed Beam pipelines. |
| EMR | Dataproc | De-emphasized vs serverless analytics. |

**Exam tip**  
> Large-scale SQL analytics → BigQuery  
> Streaming pipelines → Dataflow

---

## Configuration and Runtime Management

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| SSM Parameter Store | Env Vars + Secret Manager | GCP favors env-based config. |
| Auto Scaling Groups | Managed Instance Groups | Mostly background knowledge. |
| Launch Templates | Instance Templates | Secondary topic. |

---

## Key Exam Heuristics (Expanded)

- **Cloud Run is the default answer** for containerized workloads.
- **Firestore beats Cloud SQL** unless relational constraints are explicit.
- **Spanner beats everything** for global consistency.
- **Pub/Sub underpins almost all async designs**.
- **Eventarc is preferred for platform and audit events**.
- **Service Accounts + Workload Identity** over keys or secrets.
- **Global load balancing is assumed**, not a design choice.
- **Opinionated managed services beat configurable ones**.
- **Kubernetes is never the default** without a strong justification.
- **Structured logging is expected**, not optional.
- **Cost-optimized storage tiers are frequently tested**.
- If two answers work, **pick the one with less operational responsibility**.

---
