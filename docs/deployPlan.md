# FastTriage AWS Deploy Plan (Technical Guide)

This guide turns your current “single Docker host” architecture (Backend + RabbitMQ + LLM Summarizer Worker) into a production-grade AWS deployment while preserving the same data flows:

* **Twilio WhatsApp → HTTPS webhook → Backend (FastAPI)**
* **Backend → publish job (AMQP) → RabbitMQ**
* **LLM worker → consume job → call IBM watsonx → upsert MongoDB → store PDFs in S3**
* **Frontend/doctor download → Backend → S3 presigned URL**

---

## 1) Target AWS Architecture Mapping

### Recommended AWS services

* **Compute (containers):** Amazon **ECS on Fargate**
* **Images:** Amazon **ECR**
* **Public ingress (HTTPS):** **ALB** (Application Load Balancer) + **ACM** certificate + **Route 53**
* **Messaging (RabbitMQ):** **Amazon MQ (RabbitMQ engine)** *(recommended vs self-hosting RabbitMQ)*
* **Database (MongoDB):** Choose one:

  * **MongoDB Atlas** (managed, easiest) **OR**
  * **Amazon DocumentDB** (Mongo-compatible, AWS-native; can be costly) **OR**
  * Self-managed Mongo on EC2 (not recommended unless you really need it)
* **Artifacts:** **S3** bucket for PDFs + lifecycle policies
* **Secrets:** **AWS Secrets Manager** (Twilio token, RabbitMQ creds, Mongo URI, IBM watsonx creds)
* **Observability:** **CloudWatch Logs**, metrics/alarms, **X-Ray** (optional)
* **Network:** **VPC** with public/private subnets + **NAT Gateway** for outbound calls (watsonx, Twilio APIs if needed)

---

## 2) Environments & Naming

Create at least two environments:

* `dev` (cheap/smaller)
* `prod` (hardened)

Suggested naming pattern:

* VPC: `fasttriage-prod-vpc`
* ECS cluster: `fasttriage-prod-cluster`
* ECR repos: `fasttriage-backend`, `fasttriage-llm-worker`
* ALB: `fasttriage-prod-alb`
* S3 bucket: `fasttriage-prod-artifacts-<unique-suffix>`

---

## 3) Networking (VPC Design)

### Subnets

Use **2 AZs minimum** for redundancy.

* **Public subnets (2x):**

  * ALB
  * NAT Gateway (one per AZ is ideal; one total is cheaper but less resilient)

* **Private subnets (2x):**

  * ECS tasks (backend + worker)
  * Amazon MQ (RabbitMQ)
  * DocumentDB (if you use it)

### Routing

* Public subnets route `0.0.0.0/0` → **Internet Gateway**
* Private subnets route `0.0.0.0/0` → **NAT Gateway** (outbound only)

### Security Groups (high level)

* `sg-alb`:

  * Inbound: `443` from `0.0.0.0/0`
  * Outbound: to `sg-backend` on backend port (e.g., `8000`)

* `sg-backend`:

  * Inbound: `8000` from `sg-alb`
  * Outbound:

    * to `sg-amq` on `5672`
    * to MongoDB (Atlas/DocDB)
    * to S3 (via NAT or VPC endpoint)
    * (optional) Twilio API / IBM watsonx endpoints

* `sg-worker`:

  * Inbound: none (no public exposure)
  * Outbound:

    * to `sg-amq` on `5672`
    * to MongoDB
    * to S3
    * to IBM watsonx (via NAT)

* `sg-amq` (Amazon MQ RabbitMQ):

  * Inbound: `5672` from `sg-backend` and `sg-worker`
  * Inbound: `15672` only from a restricted admin source (VPN/bastion) or not at all in prod

---

## 4) Containerization & Image Publishing (ECR)

### ECR repositories

Create two repos:

* `fasttriage-backend`
* `fasttriage-llm-worker`

### Build & push (example)

```bash
AWS_REGION=us-east-1
AWS_ACCOUNT_ID=123456789012

aws ecr get-login-password --region "$AWS_REGION" \
  | docker login --username AWS --password-stdin \
    "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com"

docker build -t fasttriage-backend:latest ./backend
docker tag fasttriage-backend:latest \
  "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/fasttriage-backend:latest"
docker push "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/fasttriage-backend:latest"

docker build -t fasttriage-llm-worker:latest ./llm_summarizer_service
docker tag fasttriage-llm-worker:latest \
  "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/fasttriage-llm-worker:latest"
docker push "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/fasttriage-llm-worker:latest"
```

---

## 5) Messaging Layer (RabbitMQ)

### Recommended: Amazon MQ (RabbitMQ)

Why:

* RabbitMQ is stateful; running it on Fargate requires extra persistence (EFS) and careful ops.
* Amazon MQ gives you managed storage, patching, monitoring, and simpler reliability.

**Setup notes:**

* Deploy Amazon MQ RabbitMQ into **private subnets**
* Enable **TLS** if possible
* Store credentials in Secrets Manager
* Use the broker endpoint as `amqps://...:5671` (or `amqp://...:5672` if not using TLS)

---

## 6) Database (MongoDB / Triage Data)

### Option A (often best): MongoDB Atlas

* Private networking possible (VPC peering / private endpoint) if needed
* Operationally simplest
* Set IP access to **AWS NAT EIP(s)** or use private connectivity

### Option B: Amazon DocumentDB

* Put in private subnets
* Requires a subnet group + security group rules from ECS tasks
* Validate driver compatibility with your Mongo features

### Minimal baseline schema concerns

* Use unique indexes for “triage session id” / “patient id + timestamp”
* Ensure idempotency for upserts (worker retries)

---

## 7) S3 (PDF Artifacts & Summaries)

### Bucket configuration

* Block Public Access: **ON**
* Encryption: SSE-S3 or SSE-KMS
* Lifecycle:

  * Move old PDFs to IA/Glacier after N days (optional)
* Prefix structure suggestion:

  * `triage/<summary_id>/report.pdf`
  * `triage/<summary_id>/raw.json` (optional, if you store structured summary)

### Presigned URL flow

Backend generates presigned URLs with an IAM role that has scoped permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ArtifactsReadWrite",
      "Effect": "Allow",
      "Action": ["s3:PutObject","s3:GetObject"],
      "Resource": "arn:aws:s3:::fasttriage-prod-artifacts-*/triage/*"
    }
  ]
}
```

---

## 8) Secrets & Configuration

### Store in AWS Secrets Manager

Typical secrets:

* `TWILIO_AUTH_TOKEN`
* `TWILIO_ACCOUNT_SID`
* `MONGO_URI`
* `AMQ_RABBITMQ_URI` (or host/user/pass)
* `IBM_WATSONX_API_KEY` (or IAM token config)
* Any JWT signing keys or internal API keys

### Inject into ECS

Use task definition `secrets` to map Secrets Manager keys → env vars.

---

## 9) ECS on Fargate (Core Deployment)

You will deploy **two ECS services**:

1. **Backend service**

* Desired count: 1 (dev) / 2+ (prod)
* Behind ALB
* Health check endpoint: `/health` (implement it)
* CPU/mem: sized for web + quick IO (e.g., 0.5 vCPU / 1GB as starting point)

2. **LLM worker service**

* Desired count: 1 (since you want “one at a time” CPU/memory intensive jobs)
* No ALB
* CPU/mem: larger (e.g., 2 vCPU / 4–8GB depending on model call + PDF generation)

### Worker concurrency control

To ensure “process one job at a time”:

* Configure the worker to use **prefetch=1** in RabbitMQ (QoS) and run a single consumer process/thread.
* Alternatively run multiple tasks but each with prefetch=1 (scales horizontally if you ever want it).

---

## 10) Public Ingress (ALB + HTTPS + Route53 + ACM)

### Steps

1. Register/choose a domain in Route53 (or external DNS).
2. Request an ACM cert in the ALB region (e.g., `api.fasttriage.yourdomain.com`).
3. Create an ALB:

   * Listener `443` → target group → ECS backend service
4. Configure the target group:

   * Protocol HTTP
   * Port `8000` (container port)
   * Health check: `GET /health`

### Twilio Webhook

Set Twilio WhatsApp webhook to:

* `https://api.fasttriage.yourdomain.com/webhook/twilio` (example route)

**Important:** Verify Twilio signature in your backend to prevent spoofed requests.

---

## 11) IAM Roles (Least Privilege)

### ECS Task Role (Backend)

* `s3:GetObject` for downloads + `s3:PutObject` if backend writes artifacts
* `secretsmanager:GetSecretValue` for needed secrets
* `logs:*` minimal for CloudWatch logging

### ECS Task Role (Worker)

* `s3:PutObject` (PDF upload) + optional `s3:GetObject`
* `secretsmanager:GetSecretValue`
* `logs:*`
* (Optional) KMS decrypt if using SSE-KMS

---

## 12) Observability & Operations

### CloudWatch Logs

* One log group per service:

  * `/ecs/fasttriage-prod-backend`
  * `/ecs/fasttriage-prod-worker`

### Metrics & alarms (minimum)

* ALB:

  * 5XX count > threshold
  * Target health < desired count
* ECS:

  * CPU > 80% (sustained)
  * Memory > 80% (sustained)
* Amazon MQ:

  * Queue depth (if available) / broker CPU/mem
* Custom app metrics (recommended):

  * jobs published / jobs completed / job failed
  * avg processing time

### Tracing (optional but helpful)

* AWS X-Ray or OpenTelemetry collector sidecar
* Correlate `summary_id` across logs (backend → worker)

---

## 13) CI/CD (GitHub Actions → ECR → ECS)

### Pipeline outline

On push to `main`:

1. Run tests/lint (optional but recommended)
2. Build images
3. Push to ECR with tag = git sha
4. Update ECS task definition (new image tag)
5. Deploy ECS service (rolling update)

**Safe deployment settings**

* Backend: rolling update with minimum healthy percent 50–100
* Worker: update one at a time (desired count 1)

---

## 14) Deployment Runbook (Step-by-Step)

### Phase 0 — Prereqs

* AWS account, IAM admin for setup
* Domain + DNS control
* Twilio WhatsApp configured
* IBM watsonx credentials ready
* MongoDB endpoint ready (Atlas/DocumentDB)

### Phase 1 — Create Base Infrastructure

* VPC + subnets + route tables
* NAT gateway
* Security groups
* S3 bucket (+ encryption + lifecycle)
* Secrets in Secrets Manager
* (Optional) VPC endpoints for S3 to reduce NAT usage

### Phase 2 — Provision Messaging & DB

* Amazon MQ RabbitMQ broker in private subnets
* MongoDB connectivity:

  * Atlas: allow NAT EIP(s) or private endpoint
  * DocumentDB: subnet group + SG rules

### Phase 3 — ECS & ALB

* Create ECS cluster
* Create ALB + target group + ACM cert + Route53 record
* Define ECS task definitions:

  * backend task definition
  * worker task definition
* Create ECS services:

  * backend service attached to ALB
  * worker service standalone

### Phase 4 — Configure Twilio

* Point webhook to ALB domain route
* Validate signature checks
* Perform end-to-end test:

  * WhatsApp message → backend receives → job enqueued → worker processes → Mongo upsert → S3 PDF

### Phase 5 — Harden

* Enable WAF on ALB (optional)
* Tighten SG rules
* Rotate secrets
* Add alarms

---

## 15) Rollback Strategy

* Use immutable image tags (git sha).
* ECS deployments allow you to redeploy the previous task definition revision quickly.
* Keep the last N task definition revisions.
* For DB changes: use backward-compatible migrations.

---

## 16) Cost & Practical Notes

* NAT Gateway can be a meaningful cost driver (data processing + hourly). If you expect large S3 traffic, add an **S3 VPC Endpoint**.
* Amazon MQ and DocumentDB can be non-trivial costs. If you’re cost-sensitive in dev:

  * Use **Atlas** for Mongo
  * Use **smaller Amazon MQ** instance or even self-host RabbitMQ on a small EC2 **only for dev**

---

## 17) Minimal “Production Readiness” Checklist

* [ ] `/health` endpoint returns 200 fast
* [ ] Twilio signature verification enabled
* [ ] Secrets not stored in images or plaintext env files
* [ ] S3 bucket private + encrypted
* [ ] IAM least privilege (task roles separated)
* [ ] Worker idempotency (safe retries)
* [ ] Logs include `summary_id` / `job_id` correlation IDs
* [ ] Alarms for 5XX, unhealthy targets, worker failures

---

If you tell me whether you prefer **Terraform**, **CDK**, or “console-first then automate,” I can translate this plan into a concrete infrastructure layout (modules/stacks) and ECS task/service definitions aligned with your exact ports/env vars (e.g., your current `8002→8000`, AMQP `5672`, mgmt `15672`).
