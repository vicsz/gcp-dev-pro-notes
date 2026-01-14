# GCP Developer Pro for AWS Developers
_A focused AWS → GCP service mapping and exam heuristic guide for the Google Professional Cloud Developer exam_

This document maps AWS services to their GCP equivalents **only where relevant to the Professional Cloud Developer exam**, with explicit notes on where AWS instincts commonly lead to incorrect answers.

---

## Compute and Application Runtimes

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| EC2 | Compute Engine | Traditional VM-based workloads. Rarely the preferred exam answer. |
| ECS (Fargate) | Cloud Run | **Primary container runtime**. Serverless, scale-to-zero, no cluster management. |
| EKS | GKE | Correct only when Kubernetes-specific features are explicitly required. |
| Lambda | Cloud Functions | Event-driven functions; often secondary to Cloud Run. |
| Elastic Beanstalk | App Engine | Legacy/simple workloads. Often a distractor. |

---

### Cloud Run (Very High Exam Weight)

**Cloud Run characteristics**
- Runs stateless containers
- Fully managed, serverless
- Scale-to-zero
- HTTP-based
- Strong integration with Pub/Sub, Eventarc, Workflows

**Key Cloud Run limits (Exam-Relevant)**
- **Max request duration:** up to **60 minutes**
- **Concurrency:** configurable (default >1)
- **Execution model:** request-based, not event-loop-based

**Exam takeaway**
> Long-running requests do **not** disqualify Cloud Run  
> (unlike AWS Lambda’s hard 15-minute limit)

---

### Cloud Functions

**Cloud Functions characteristics**
- Event-driven, function-as-a-service
- Lower operational overhead than Cloud Run
- Tighter execution constraints

**Key Cloud Functions limits (Exam-Relevant)**
- **Shorter max execution time** than Cloud Run (varies by generation)
- Less control over concurrency and runtime behavior

**Exam takeaway**
> If execution time is non-trivial or containerization is mentioned → **Cloud Run**

---

### Cloud Run vs AWS Lambda (Critical Difference)

| Aspect | AWS Lambda | Cloud Run |
|---|---|---|
| Max runtime | 15 minutes | **Up to 60 minutes** |
| Execution model | Event-based | HTTP request-based |
| Concurrency | Managed implicitly | Explicit and configurable |
| Packaging | Zip or container | **Container-first** |

**Exam implication**
> Workloads that would exceed Lambda limits are **still valid Cloud Run candidates**

---

### GKE (Medium Exam Weight)

**GKE characteristics**
- Managed Kubernetes
- High flexibility
- Higher operational complexity

**When GKE is the correct answer**
- Need Kubernetes-native features
- Custom networking or sidecars
- GPU workloads
- Stateful workloads with tight control

**Exam trap**
> “Enterprise-grade” ≠ Kubernetes  
> GKE is *never* the default answer

---

### Compute Engine (Low Exam Weight)

**Compute Engine characteristics**
- Full VM control
- Highest operational burden

**Typical exam framing**
- Legacy app migration
- OS-level customization required

**Exam takeaway**
> If Compute Engine appears, the question usually *forces* it

---

### App Engine (Low Exam Weight)

**App Engine characteristics**
- Platform-as-a-Service
- Opinionated runtime environments

**Exam framing**
- Simple apps
- Legacy Google workloads

**Exam trap**
> App Engine often appears as a plausible but outdated option

---

## Compute Selection Heuristics (Exam-Critical)

Use these aggressively:

- **Containerized app** → Cloud Run
- **Event-driven + short execution** → Cloud Functions
- **Long-running HTTP workloads** → Cloud Run
- **Kubernetes-specific needs** → GKE
- **Legacy VM-based workloads** → Compute Engine
- **Minimal ops preferred** → Cloud Run over everything else

---

## Common AWS → GCP Exam Traps (With Correct Choices)

- **Rejecting Cloud Run due to execution time**  
  **Why this is wrong:** Cloud Run supports up to 60-minute requests.  
  **Correct choice:** **Cloud Run** for long-running containerized workloads.

- **Defaulting to GKE for “serious” applications**  
  **Why this is wrong:** The exam prioritizes managed, serverless platforms.  
  **Correct choice:** **Cloud Run**, unless Kubernetes features are explicitly required.

- **Choosing Cloud Functions for containerized workloads**  
  **Why this is wrong:** Cloud Functions are function-first, not container-first.  
  **Correct choice:** **Cloud Run** when containers are mentioned.

- **Using Compute Engine when scaling or availability is required**  
  **Why this is wrong:** VMs require manual scaling and management.  
  **Correct choice:** **Cloud Run** or **GKE**, depending on requirements.

- **Assuming App Engine is the modern default**  
  **Why this is wrong:** App Engine is no longer Google’s primary app platform.  
  **Correct choice:** **Cloud Run**.

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
| SQS | Pub/Sub | Pub/Sub replaces traditional queues. Core async primitive. |
| SNS | Pub/Sub | Fan-out is native. One service covers pub and sub patterns. |
| EventBridge | Eventarc | Routes platform and audit-log events to Cloud Run. |
| Kinesis | Dataflow | Managed streaming and batch data pipelines. |

---

### Pub/Sub (Very High Exam Weight)

**Pub/Sub characteristics**
- Fully managed messaging service
- Global by default
- Supports **push and pull** subscriptions
- At-least-once delivery
- Native integration with Cloud Run, Cloud Functions, Dataflow

**Pub/Sub replaces in AWS terms**
- SQS (queues)
- SNS (topics / fan-out)
- EventBridge (application-level events)

**Common exam use cases**
- Asynchronous processing
- Decoupling microservices
- Fan-out to multiple consumers
- Event-driven Cloud Run triggers

**Exam takeaway**
> If the architecture is async, decoupled, or event-driven → **Pub/Sub**

---

### Push vs Pull Subscriptions (Exam-Relevant)

- **Push**  
  - Pub/Sub pushes events to an HTTP endpoint  
  - Common with **Cloud Run**
- **Pull**  
  - Consumers poll for messages  
  - Used with Dataflow or custom workers

**Exam tip**
> Cloud Run + Pub/Sub → push is usually implied

---

### Eventarc (High Exam Weight)

**Eventarc characteristics**
- Event routing service
- Sources include:
  - Cloud Storage events
  - Pub/Sub
  - Cloud Audit Logs
- Targets:
  - Cloud Run (primary)
  - Cloud Functions

**What Eventarc is NOT**
- A message queue
- A streaming service

**Common exam use cases**
- Reacting to platform events (file uploads, resource changes)
- Routing audit-log-based events
- Triggering Cloud Run services without custom plumbing

**Exam takeaway**
> Platform or infrastructure events → **Eventarc**, not Pub/Sub directly

---

### Eventarc vs Pub/Sub (Critical Exam Distinction)

| Use Case | Correct Choice |
|---|---|
| Application-generated events | Pub/Sub |
| Platform or system events | Eventarc |
| Simple async messaging | Pub/Sub |
| Audit log–based triggers | Eventarc |

---

### Dataflow (Medium Exam Weight)

**Dataflow characteristics**
- Managed Apache Beam
- Handles **streaming and batch**
- Exactly-once semantics (conceptually)
- Used for large-scale data processing

**Common exam use cases**
- Stream processing from Pub/Sub
- ETL pipelines
- Windowed aggregations

**Exam takeaway**
> Dataflow is for **data pipelines**, not application messaging

---

### Workflows (Related, Often Paired)

**Workflows characteristics**
- YAML-based service orchestration
- Coordinates calls between services
- Deterministic control flow

**Workflows vs Pub/Sub**
- Pub/Sub = asynchronous event delivery
- Workflows = ordered, multi-step orchestration

**Exam takeaway**
> Multi-step service logic → Workflows  
> Event notification → Pub/Sub

---

## Messaging Selection Heuristics (Exam-Critical)

Use these aggressively:

- **Async communication** → Pub/Sub
- **Fan-out** → Pub/Sub
- **Platform-generated events** → Eventarc
- **Audit log–based triggers** → Eventarc
- **Streaming analytics** → Dataflow
- **Multi-step orchestration** → Workflows

---

## Common AWS → GCP Exam Traps (With Correct Choices)

- **Using Pub/Sub for platform events**  
  **Why this is wrong:** Pub/Sub is for application messaging, not system events.  
  **Correct choice:** **Eventarc**.

- **Using Workflows as a message queue**  
  **Why this is wrong:** Workflows orchestrate services; they do not deliver events.  
  **Correct choice:** **Pub/Sub**.

- **Using Dataflow for simple async messaging**  
  **Why this is wrong:** Dataflow is heavyweight and pipeline-oriented.  
  **Correct choice:** **Pub/Sub**.

- **Overthinking SQS vs SNS equivalents**  
  **Why this is wrong:** Pub/Sub replaces both patterns.  
  **Correct choice:** **Pub/Sub**.

- **Manually wiring event triggers**  
  **Why this is wrong:** Eventarc exists to remove custom plumbing.  
  **Correct choice:** **Eventarc + Cloud Run**.

---

### One-Line Exam Mantra

> **Pub/Sub moves messages.  
> Eventarc routes platform events.  
> Dataflow processes data.  
> Workflows coordinates services.**

---

## Analytics and Data (Developer-Relevant)

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| Athena | BigQuery | **Very important**. Serverless, SQL-first analytics engine. |
| Glue | Dataflow | Managed Apache Beam pipelines for batch and streaming. |
| EMR | Dataproc | Hadoop/Spark clusters; de-emphasized vs serverless options. |

---

### BigQuery (Very High Exam Weight)

**BigQuery characteristics**
- Fully serverless
- Columnar storage
- ANSI SQL
- Automatically scalable
- No infrastructure management

**What BigQuery is used for**
- Analytics and reporting
- Large-scale SQL queries
- Data exploration
- BI dashboards

**What BigQuery is NOT**
- Transactional (OLTP)
- Low-latency application queries
- Primary application datastore

**Exam takeaway**
> Large datasets + SQL + analytics → **BigQuery**

---

### BigQuery vs Athena (Exam-Relevant Differences)

| Aspect | Athena | BigQuery |
|---|---|---|
| Infrastructure | Serverless | Serverless |
| Storage | S3-backed | Native managed storage |
| Performance model | Query-per-file | Columnar, optimized |
| Integration | Query engine | Analytics platform |

**Exam implication**
> BigQuery is not just “Athena on GCP” — it’s a **central analytics service**

---

### Dataflow (Medium–High Exam Weight)

**Dataflow characteristics**
- Managed Apache Beam
- Unified model for:
  - Batch processing
  - Streaming processing
- Exactly-once semantics (conceptually)
- Tight integration with Pub/Sub and BigQuery

**Common exam use cases**
- Streaming analytics
- Event aggregation
- ETL pipelines
- Processing Pub/Sub streams

**Exam takeaway**
> Streaming data pipelines → **Dataflow**

---

### Dataflow vs Pub/Sub (Critical Exam Distinction)

| Use Case | Correct Choice |
|---|---|
| Deliver messages | Pub/Sub |
| Process or transform data | Dataflow |
| Fan-out | Pub/Sub |
| Windowed aggregation | Dataflow |

**Exam trap**
> Treating Pub/Sub as a processing engine

---

### Dataproc (Low Exam Weight)

**Dataproc characteristics**
- Managed Hadoop / Spark clusters
- VM-based
- More operational overhead

**Exam framing**
- Lift-and-shift of existing Hadoop/Spark workloads
- Legacy data processing systems

**Exam takeaway**
> New analytics workloads → **not Dataproc**  
> Existing Hadoop/Spark → Dataproc

---

## Analytics Selection Heuristics (Exam-Critical)

Use these rules aggressively:

- **Ad-hoc or large-scale SQL queries** → BigQuery
- **Dashboards and reporting** → BigQuery
- **Streaming analytics** → Dataflow
- **Batch ETL pipelines** → Dataflow
- **Existing Spark/Hadoop** → Dataproc
- **Application queries** → Not BigQuery

---

## Common AWS → GCP Exam Traps (With Correct Choices)

- **Using BigQuery for application reads/writes**  
  **Why this is wrong:** BigQuery is optimized for analytics, not OLTP.  
  **Correct choice:**  
  - **Firestore** for NoSQL OLTP  
  - **Cloud SQL** for relational OLTP  
  - **Spanner** for global OLTP

- **Using Pub/Sub for data processing**  
  **Why this is wrong:** Pub/Sub moves data; it does not transform it.  
  **Correct choice:** **Dataflow**.

- **Choosing Dataproc for new workloads**  
  **Why this is wrong:** Dataproc requires cluster management.  
  **Correct choice:** **BigQuery** or **Dataflow**.

- **Treating BigQuery as Athena-with-different-APIs**  
  **Why this is wrong:** BigQuery is a full analytics platform.  
  **Correct choice:** **BigQuery as the analytics center**.

---

### One-Line Exam Mantra

> **BigQuery queries data.  
> Dataflow processes data.  
> Pub/Sub moves data.**

---

## APIs, Traffic, and Integration

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| API Gateway | API Gateway | OpenAPI-driven, simpler and more opinionated than AWS. |
| ALB / NLB / Global Accelerator | Cloud Load Balancing | **Single global load balancing system**, not multiple distinct services. |
| Step Functions | Workflows | YAML-based service orchestration. Frequently tested. |

---

### API Gateway (Medium Exam Weight)

**GCP API Gateway characteristics**
- OpenAPI specification–driven
- Used to expose REST APIs
- Integrates with Cloud Run, Cloud Functions, and GKE
- Handles auth, quotas, and routing

**How this differs from AWS**
- Fewer feature variants
- Less emphasis on request/response transformation
- Often paired with Cloud Run

**Exam takeaway**
> If the question mentions OpenAPI, REST exposure, or API management → **API Gateway**

---

### Cloud Load Balancing (High Exam Weight)

**Key difference vs AWS**
> **GCP does NOT split load balancing into ALB / NLB / Global Accelerator equivalents.**  
> Cloud Load Balancing is a **single, unified global service** with multiple modes.

---

### Cloud Load Balancing Modes (Exam-Relevant)

| AWS Mental Model | GCP Equivalent | Notes |
|---|---|---|
| ALB (HTTP/HTTPS) | HTTP(S) Load Balancer | Most common. Global by default. |
| NLB (TCP/UDP) | TCP/UDP Load Balancer | Used for non-HTTP traffic. |
| Global Accelerator | Built-in | Global anycast IPs are standard. |

**Exam-critical distinctions**
- Global HTTP(S) LB is **default**, not special
- SSL termination is assumed
- Works natively with:
  - Cloud Run
  - GKE
  - Compute Engine

---

### Cloud Load Balancing vs AWS ELB Family (Exam Trap)

**AWS instinct**
- Choose ALB vs NLB vs GA carefully

**GCP exam reality**
- You choose **traffic type**, not a product
- Global vs regional is usually implicit
- HTTP(S) is assumed unless stated otherwise

**Exam takeaway**
> Do NOT overthink load balancer selection — Cloud Load Balancing “just works” globally

---

### Workflows (High Exam Weight)

**Workflows characteristics**
- YAML-based orchestration
- Coordinates calls to services and APIs
- Deterministic, ordered execution
- Native auth via service accounts

**Workflows replaces in AWS terms**
- Step Functions (Standard)

**Common exam use cases**
- Calling multiple Cloud Run services
- Orchestrating API calls
- Coordinating retries and branching logic

**Exam takeaway**
> Multi-step logic with service calls → **Workflows**

---

### Workflows vs Pub/Sub (Critical Exam Distinction)

| Use Case | Correct Choice |
|---|---|
| Fire-and-forget messaging | Pub/Sub |
| Fan-out | Pub/Sub |
| Ordered, multi-step execution | Workflows |
| Conditional logic | Workflows |
| Long-running coordination | Workflows |

---

### API Gateway vs Cloud Load Balancing (Exam Trap)

| Need | Correct Choice |
|---|---|
| API definition & management | API Gateway |
| Traffic routing & SSL | Cloud Load Balancing |
| Public REST APIs | API Gateway |
| Backend service exposure | Cloud Load Balancing |

**Exam takeaway**
> API Gateway manages APIs  
> Load Balancer manages traffic

---

## Traffic and Integration Heuristics (Exam-Critical)

Use these aggressively:

- **Public REST API** → API Gateway + Cloud Run
- **HTTP traffic routing** → Cloud Load Balancing
- **Global traffic** → Cloud Load Balancing (default)
- **Multi-step orchestration** → Workflows
- **Event delivery** → Pub/Sub
- **Platform events** → Eventarc

---

## Common AWS → GCP Exam Traps (With Correct Choices)

- **Choosing between ALB and NLB equivalents**  
  **Why this is wrong:** GCP abstracts this behind a single load balancing system.  
  **Correct choice:** **Cloud Load Balancing**, selecting HTTP(S) unless stated otherwise.

- **Using Pub/Sub for orchestration**  
  **Why this is wrong:** Pub/Sub delivers events but does not manage control flow.  
  **Correct choice:** **Workflows**.

- **Using API Gateway for simple traffic routing**  
  **Why this is wrong:** API Gateway is for API management, not general routing.  
  **Correct choice:** **Cloud Load Balancing**.

- **Assuming Global Accelerator is a separate service**  
  **Why this is wrong:** Global anycast IPs are built in.  
  **Correct choice:** **Cloud Load Balancing**.

---

### One-Line Exam Mantra

> **API Gateway defines APIs.  
> Cloud Load Balancing moves traffic globally.  
> Workflows orchestrates services.**

---

## Networking (Light but Testable)

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| VPC | VPC | **Global network**; subnets are regional. |
| Security Groups | Firewall Rules | Applied at VPC level, not instance level. |
| PrivateLink | Private Service Connect | Private access to Google-managed services. |
| NAT Gateway | Cloud NAT | Provides outbound internet for private resources. |
| Route 53 | Cloud DNS | Managed DNS service; rarely tricky. |

---

### VPC (Exam-Relevant Differences)

**GCP VPC characteristics**
- **Global by default**
- Subnets are **regional**
- Resources in different regions can share the same VPC
- No VPC peering required between regions in the same VPC

**AWS vs GCP mental shift**
- AWS VPCs are regional
- GCP VPCs are global

**Exam takeaway**
> If the question involves multi-region resources in one network → **single GCP VPC**

---

### Subnets (Conceptual Only)

- Subnets belong to a **region**
- IP ranges are defined per subnet
- No AZ-specific subnets like AWS

**Exam takeaway**
> Zone-level subnet design is not a thing in GCP

---

### Firewall Rules (Very Light but Testable)

**Firewall rule characteristics**
- Defined at the **VPC level**
- Applied via:
  - Network tags
  - Service accounts
- Stateless by default (conceptually)

**AWS trap**
> Looking for security groups attached to instances

**Correct choice**
> **Firewall rules**, scoped by tags or service accounts

---

### Private Service Connect (Medium Exam Weight)

**Private Service Connect characteristics**
- Private access to:
  - Google APIs
  - Google-managed services
  - Producer VPC services
- No public IP exposure

**AWS mental model**
- PrivateLink equivalent

**Exam use cases**
- Accessing managed services privately
- Reducing public internet exposure

**Exam takeaway**
> Private access to managed services → **Private Service Connect**

---

### Cloud NAT (Low Exam Weight)

**Cloud NAT characteristics**
- Provides outbound internet access
- Used by private Compute Engine or GKE nodes
- Serverless products usually do not require it

**Exam trap**
> Adding NAT for Cloud Run or Cloud Functions

**Correct choice**
> Cloud NAT only for VM-based workloads

---

### Cloud DNS (Low Exam Weight)

**Cloud DNS characteristics**
- Managed DNS service
- Public and private zones
- Conceptually identical to Route 53

**Exam takeaway**
> DNS is rarely the deciding factor

---

## Networking Selection Heuristics (Exam-Critical)

Use these rules:

- **Multi-region resources, same network** → single VPC
- **Private access to managed services** → Private Service Connect
- **Outbound internet from private VMs** → Cloud NAT
- **Traffic filtering** → Firewall rules
- **Serverless networking concerns** → usually abstracted away

---

## Common AWS → GCP Exam Traps (With Correct Choices)

- **Creating multiple VPCs for multiple regions**  
  **Why this is wrong:** GCP VPCs are global.  
  **Correct choice:** **Single VPC with regional subnets**.

- **Looking for Security Groups**  
  **Why this is wrong:** GCP does not attach firewalls to instances.  
  **Correct choice:** **Firewall rules** with tags or service accounts.

- **Using Cloud NAT for serverless workloads**  
  **Why this is wrong:** Serverless products manage outbound connectivity.  
  **Correct choice:** **No NAT required**.

- **Exposing managed services publicly by default**  
  **Why this is wrong:** Private access is preferred.  
  **Correct choice:** **Private Service Connect**.

---

### One-Line Exam Mantra

> **VPCs are global.  
> Subnets are regional.  
> Firewall rules are VPC-wide.  
> Serverless hides networking.**

---

## Identity and Security (Developer Scope)

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| IAM Roles | Service Accounts | **Extremely important**. Every workload runs as a service account. |
| STS | Workload Identity | Replaces AssumeRole and long-lived credentials. |
| Secrets Manager | Secret Manager | Used for sensitive values; injected at runtime. |
| KMS | Cloud KMS | Used for CMEK and envelope encryption. |

---

### Service Accounts (Very High Exam Weight)

**Service Account characteristics**
- Identity for:
  - Cloud Run
  - Cloud Functions
  - GKE workloads
  - Compute Engine VMs
- Attached directly to workloads
- Scoped via IAM roles

**AWS mental shift**
- No equivalent of “instance profile + STS dance”
- Identity is **bound at deployment time**

**Exam takeaway**
> If a workload accesses another service → **service account permissions**

---

### Workload Identity (Very High Exam Weight)

**Workload Identity characteristics**
- Allows workloads to authenticate **without keys**
- Integrates with:
  - Cloud Run
  - GKE
  - External identity providers
- Short-lived credentials issued automatically

**Replaces**
- IAM access keys
- Embedded credentials
- Manual secret rotation

**Exam takeaway**
> Any mention of credentials → Workload Identity

---

### Secret Manager (High Exam Weight)

**Secret Manager characteristics**
- Stores sensitive values (API keys, passwords)
- Integrated with IAM
- Versioned secrets
- Injected as:
  - Environment variables
  - Files

**Exam distinction**
- Secrets ≠ configuration
- Configuration often uses env vars directly

**Exam takeaway**
> Sensitive values → Secret Manager  
> Non-sensitive config → environment variables

---

### Cloud KMS (Medium Exam Weight)

**Cloud KMS characteristics**
- Manages encryption keys
- Used for:
  - Customer-managed encryption keys (CMEK)
  - Envelope encryption
- Integrates with Cloud Storage, BigQuery, Compute Engine, etc.

**Exam framing**
- Compliance requirements
- Regulatory control over encryption keys

**Exam takeaway**
> If compliance or customer-managed keys are mentioned → **Cloud KMS**

---

### Identity-First Security Model (Critical Exam Concept)

**GCP security philosophy**
- Identity-based access first
- Network controls second
- Keys last (ideally never)

**AWS trap**
> Solving security with VPCs, firewalls, or IP allowlists

**Correct approach**
> IAM roles on service accounts

---

## Security Selection Heuristics (Exam-Critical)

Use these rules aggressively:

- **Service-to-service access** → Service Accounts
- **No static credentials** → Workload Identity
- **Sensitive data** → Secret Manager
- **Compliance / key ownership** → Cloud KMS
- **Least privilege** → Narrow IAM roles on service accounts

---

## Common AWS → GCP Exam Traps (With Correct Choices)

- **Embedding credentials in code or config files**  
  **Why this is wrong:** Violates GCP identity-first design.  
  **Correct choice:** **Workload Identity + Service Accounts**.

- **Using network-level security for service access**  
  **Why this is wrong:** GCP prioritizes identity-based access.  
  **Correct choice:** **IAM roles on service accounts**.

- **Using Secret Manager for all configuration**  
  **Why this is wrong:** Secrets are for sensitive data only.  
  **Correct choice:** **Env vars for config, Secret Manager for secrets**.

- **Manually rotating credentials**  
  **Why this is wrong:** Workload Identity provides short-lived credentials automatically.  
  **Correct choice:** **Workload Identity**.

- **Assuming KMS is optional**  
  **Why this is wrong:** Many compliance scenarios require CMEK.  
  **Correct choice:** **Cloud KMS** when compliance is mentioned.

---

### One-Line Exam Mantra

> **Identity first.  
> Keys never.  
> Service accounts everywhere.**

## Observability

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| CloudWatch | Cloud Monitoring | Metrics, alerts, and **SLOs** are emphasized. |
| CloudWatch Logs | Cloud Logging | **Structured logging (JSON) is important**. |
| X-Ray | Cloud Trace | Appears conceptually (latency, request tracing). |

---

### Cloud Monitoring (High Exam Weight)

**Cloud Monitoring characteristics**
- Metrics collection
- Alerting
- Dashboards
- **Service Level Objectives (SLOs)** and error budgets

**Exam framing**
- Focuses on *outcomes* (latency, availability)
- Ties directly to reliability goals

**Exam takeaway**
> Reliability is measured via **SLOs**, not just alarms

---

### SLOs and Error Budgets (Exam-Critical Concept)

**SLO concepts**
- SLI: what you measure (latency, availability)
- SLO: target reliability (e.g., 99.9%)
- Error budget: allowable failure

**Exam trap**
> Monitoring only CPU or memory

**Correct choice**
> Monitoring user-facing metrics and defining SLOs

---

### Cloud Logging (Very High Exam Weight)

**Cloud Logging characteristics**
- Centralized log storage
- Native support for **structured logs**
- Logs are indexed and queryable

**Structured logging expectations**
- JSON format
- Severity field
- Key-value pairs

**Exam takeaway**
> Logs are data, not text blobs

---

### Cloud Logging vs Cloud Monitoring (Exam Distinction)

| Concern | Correct Service |
|---|---|
| Metrics & alerts | Cloud Monitoring |
| Logs & log queries | Cloud Logging |
| Reliability targets | Cloud Monitoring (SLOs) |

---

### Cloud Trace (Medium Exam Weight)

**Cloud Trace characteristics**
- Distributed tracing
- Latency analysis
- Request flow visualization

**Exam framing**
- Used to diagnose performance bottlenecks
- Usually paired with Cloud Run or APIs

**Exam takeaway**
> Latency or request flow issues → Cloud Trace

---

### Correlation and Context (Exam-Relevant)

**Observability on GCP assumes**
- Logs include request IDs
- Metrics correlate to services
- Traces link requests across services

**AWS trap**
> Treating logs, metrics, and traces as separate silos

**Correct approach**
> Correlated observability signals

---

## Observability Selection Heuristics (Exam-Critical)

Use these rules aggressively:

- **Reliability targets** → Cloud Monitoring + SLOs
- **Application logs** → Cloud Logging
- **Query logs** → Structured JSON logs
- **Latency diagnosis** → Cloud Trace
- **User experience monitoring** → SLIs and SLOs

---

## Common AWS → GCP Exam Traps (With Correct Choices)

- **Using unstructured log strings**  
  **Why this is wrong:** Logs must be queryable and indexable.  
  **Correct choice:** **Structured JSON logs**.

- **Alerting on infrastructure metrics only**  
  **Why this is wrong:** The exam emphasizes user-facing reliability.  
  **Correct choice:** **SLO-based alerts**.

- **Ignoring trace data when diagnosing latency**  
  **Why this is wrong:** Logs alone don’t show request flow.  
  **Correct choice:** **Cloud Trace**.

- **Treating monitoring as optional**  
  **Why this is wrong:** Observability is part of application design.  
  **Correct choice:** **Monitoring + Logging by default**.

---

### One-Line Exam Mantra

> **Metrics define reliability.  
> Logs explain behavior.  
> Traces explain latency.**

---

## CI/CD and Build

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| CodeBuild | Cloud Build | Central CI service. Common in questions. |
| CodePipeline | Cloud Build + Deploy | Composable pipeline model. |
| ECR | Artifact Registry | Stores containers and language artifacts. |

---

## Configuration and Runtime Management

| AWS | GCP | Exam-Relevant Notes |
|---|---|---|
| SSM Parameter Store | Env Vars + Secret Manager | GCP favors env-based config. |
| Auto Scaling Groups | Managed Instance Groups | Mostly background knowledge. |
| Launch Templates | Instance Templates | Secondary topic. |

---

## Question Wording Patterns (Very High Yield)

Google exam questions almost always **telegraph the correct service choice** using a small number of adjectives or constraints.  
Missing or ignoring a single word is the most common failure mode for AWS-experienced candidates.

Train yourself to **scan for these words first**, then choose the service that matches them *before* reading the options.

---

### Operational Burden Signals (Extremely High Yield)

| Wording in Question | What Google Wants | Typical Correct Choice |
|---|---|---|
| “Minimal operational overhead” | Fully managed | Cloud Run / Firestore / BigQuery |
| “No infrastructure management” | Serverless | Cloud Run |
| “Avoid managing servers” | Serverless | Cloud Run |
| “Simplest solution” | Opinionated managed service | Cloud Run / Pub/Sub |

**AWS trap**
- Choosing configurable services (GKE, Cloud SQL, Compute Engine)

---

### Scale and Growth Signals

| Wording in Question | What It Implies | Typical Correct Choice |
|---|---|---|
| “Automatically scales” | No capacity planning | Cloud Run / Firestore |
| “Unpredictable traffic” | Scale-to-zero + burst | Cloud Run |
| “High throughput” | Managed backend | Pub/Sub / Firestore |
| “Millions of requests” | Horizontal scaling | Firestore / Cloud Run |

**AWS trap**
- Thinking in ASGs or provisioned throughput

---

### Global and Consistency Signals (Critical)

| Wording in Question | What It Implies | Typical Correct Choice |
|---|---|---|
| “Global users” | Multi-region | Cloud Load Balancing |
| “Global consistency” | Strong consistency across regions | Spanner |
| “Multi-region writes” | Distributed relational DB | Spanner |
| “Low-latency worldwide” | Anycast networking | Cloud Load Balancing |

**AWS trap**
- Treating global as “replication + caching”

---

### Data Type Signals (Very High Yield)

| Wording in Question | What It Implies | Typical Correct Choice |
|---|---|---|
| “Application data” | OLTP | Firestore / Cloud SQL |
| “User profiles” | Document store | Firestore |
| “Analytics” | OLAP | BigQuery |
| “Reporting” | Query-heavy reads | BigQuery |
| “Streaming data” | Continuous processing | Dataflow |
| “Events” | Messaging | Pub/Sub |

**AWS trap**
- Using BigQuery or analytics tools for application reads

---

### Time and Execution Signals

| Wording in Question | What It Implies | Typical Correct Choice |
|---|---|---|
| “Long-running request” | > Lambda limits | Cloud Run |
| “Background processing” | Async | Pub/Sub |
| “Event-driven” | Reactive | Pub/Sub / Eventarc |
| “Workflow” | Ordered steps | Workflows |

**AWS trap**
- Rejecting Cloud Run due to runtime length

---

### Security and Identity Signals (Extremely High Yield)

| Wording in Question | What It Implies | Typical Correct Choice |
|---|---|---|
| “Avoid storing credentials” | No static secrets | Workload Identity |
| “Least privilege” | Narrow IAM | Service Accounts |
| “Secure service-to-service” | Identity-based access | Service Accounts |
| “Compliance” | Key ownership | Cloud KMS (CMEK) |

**AWS trap**
- Reaching for network controls or secrets first

---

### Cost Optimization Signals

| Wording in Question | What It Implies | Typical Correct Choice |
|---|---|---|
| “Cost-effective” | Managed + scale-to-zero | Cloud Run |
| “Infrequent access” | Cold storage | Nearline / Coldline |
| “Long-term retention” | Archival | Archive |
| “Idle most of the time” | Serverless | Cloud Run |

**AWS trap**
- Always-on resources (VMs, clusters)

---

### Integration and Architecture Signals

| Wording in Question | What It Implies | Typical Correct Choice |
|---|---|---|
| “Decoupled” | Async messaging | Pub/Sub |
| “Fan-out” | One-to-many | Pub/Sub |
| “Multi-step process” | Orchestration | Workflows |
| “Platform event” | System-triggered | Eventarc |

**AWS trap**
- Using Pub/Sub for orchestration logic

---

### Migration Signals (Sneaky but Testable)

| Wording in Question | What It Implies | Typical Correct Choice |
|---|---|---|
| “Existing workload” | Lift-and-shift | Compute Engine / Dataproc |
| “Minimal code changes” | Same runtime | Compute Engine / Cloud SQL |
| “Legacy system” | VM-based | Compute Engine |

**Exam tip**
> Migration language often overrides “modern” instincts

---

## Meta-Pattern: How to Read the Question

1. **Scan for adjectives first**
2. Highlight:
   - minimal
   - global
   - scalable
   - event-driven
   - secure
   - cost-effective
3. Decide the service **before** reading answers
4. Eliminate options that:
   - add infrastructure
   - require credentials
   - increase operational burden

---

## Final Exam Rule of Thumb

> **If Google gives you a word, they expect you to act on it.  
> Ignoring adjectives is how AWS experts fail this exam.**

---

### One-Line Exam Mantra

> **Adjectives choose the service.  
> Nouns just describe the workload.**
