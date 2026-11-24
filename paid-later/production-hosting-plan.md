# Production Hosting Plan (later, paid; not part of the current build)

| | |
|---|---|
| **Version** | 1.0, 24 Nov 2026 |
| **Status** | Plan only. The current build runs on free hosting ([LLD 17.1](../docs/lld.md#171-prototype-hosting-free-tier)). |
| **Diagram** | [Production architecture](diagrams/siliconics-production-architecture.png) |

The same container images, code, data model and tests are used. Moving to this plan changes hosting and `PROVIDER_*` settings only.

---

## 1. Client's production targets

These are the original requirements from the client's Non Functional Requirements document. The free prototype is accepted against lower targets (TRS section 8); these apply at the paid launch.

| Area | Production target |
|---|---|
| Launch capacity (NFR-CAP-02) | **1,000 requests per second** during launches with p95 under 3 s; load tests must pass at **1,500 rps** |
| Scaling (NFR-CAP-03) | Web and API auto-scale on CPU and request count; capacity pre-scaled 15 minutes before each scheduled launch |
| Availability (NFR-REL-01) | **99.9% monthly** (about 43 minutes of downtime a month) |
| Redundancy (NFR-REL-02) | At least two instances of each service across two or more availability zones; database with a synchronous standby |
| Backups (NFR-REL-04) | Point-in-time recovery for 14 days plus daily snapshots kept 35 days; RPO ≤ 5 min, RTO ≤ 1 h; quarterly restore drill |
| Firewall (NFR-SEC-01) | Managed web application firewall (OWASP Top 10, bad bots, known bad IPs) with custom rules for OTP and checkout |
| Encryption (NFR-SEC-05) | AES-256 at rest; KMS for PAN, bank details, TOTP secrets and KYC files |
| Secrets (NFR-SEC-06) | Secrets manager with rotation every 90 days |
| Security test (NFR-SEC-02) | Independent penetration test before go-live |
| Data residency (NFR-PRIV-02) | All personal data stored and processed in India (AWS Mumbai) |
| Monitoring (NFR-OBS-01) | Logs kept 30 days, metrics 13 months |

---

## 2. AWS resources (Mumbai, ap-south-1)

| Resource | Baseline | Launch mode |
|---|---|---|
| ECS Fargate `api` | 2 × (0.5 vCPU, 1 GB), autoscale on CPU 60% / RPS | pre-scale to 8, max 20 |
| ECS Fargate `web` | 2 × (1 vCPU, 2 GB) | pre-scale to 6, max 10 |
| ECS Fargate `worker` | 1 × (0.5 vCPU, 1 GB) | 2 |
| RDS PostgreSQL 16 | db.t4g.large Multi-AZ, gp3 20 GB auto-grow, PITR 14 days | optional temporary upsize to db.r6g.large; read replica for reports |
| ElastiCache Redis 7 | cache.t4g.small, 1 replica, Multi-AZ | cache.t4g.medium |
| S3 | `media`, `docs-kyc` (KMS), `invoices`, `exports` | — |
| CloudFront + AWS WAF | static files, images, ISR pages | Bot Control + waiting-room rule |
| Networking | VPC across 3 AZs, private subnets for tasks, DB and Redis, NAT, ALB in public subnets | — |

**Capacity reasoning (1,000 rps launch):** about 70% of traffic is cacheable page and asset requests served by CloudFront; `/live` and SSE reads come from Redis; only reserve and checkout writes reach PostgreSQL, bounded by deal units, per-member limits and the waiting room.

**Launch pre-scaling:** 15 minutes before `starts_at`, the scheduler raises ECS desired counts (API ×4, web ×3) and warms the `pdp:` and `launch:` Redis keys.

---

## 3. Production security controls

| Area | Control |
|---|---|
| Edge | AWS WAF managed rules (Core, Known Bad Inputs, Bot Control) + rate rules; CloudFront with TLS 1.2+; HSTS preload |
| Secrets | AWS Secrets Manager with 90-day rotation; KMS envelope encryption for `pan_enc`, `bank_enc`, `totp_secret_enc`; separate KMS key and bucket for KYC documents, access-logged |
| Database | RDS encryption at rest, TLS in transit, least-privilege app role (`ledger_entries` and `audit_log` insert/select only) |
| Uploads | ClamAV on AWS Lambda scanning every upload before it can be used |
| Privacy | All data in ap-south-1 |

---

## 4. Production settings

| Variable | Production value |
|---|---|
| `PROVIDER_PAYMENT` / `PROVIDER_SHIPPING` / `PROVIDER_PAYOUT` / `PROVIDER_KYC` | e.g. `razorpay` / `shiprocket` / `razorpayx` / chosen KYC vendor |
| `PROVIDER_SMS` / `PROVIDER_WHATSAPP` | e.g. `msg91` / `gupshup` |
| `PROVIDER_EMAIL` | e.g. `ses` |
| `STORAGE_ENDPOINT`, `STORAGE_BUCKET_*` | Amazon S3 |
| `ENCRYPTION_MASTER_KEY` | replaced by a KMS key |
| `FEATURE_TEST_INBOX` | `false` (startup refuses `true` in production mode) |
| `OTEL_EXPORTER_OTLP_ENDPOINT`, `SENTRY_DSN` | paid Grafana/CloudWatch, Sentry team plan |

---

## 5. Production delivery

- **Infrastructure as code:** Terraform modules in `infra/terraform/` (`envs/staging`, `envs/prod`).
- **Pipeline additions:** push images to ECR → `terraform plan/apply` → migrations → ECS rolling deploy with minimum healthy 100% → smoke and synthetic checks → automatic rollback.
- **Environments:** staging (production-like, anonymised data, sandbox providers) and production (tagged releases with manual approval).
- **Load test:** k6 ramp to 1,500 rps (60% browse, 25% product live data, 10% reserve, 5% checkout); assert p95 < 3 s, zero oversell, error rate < 1%.
- **Penetration test:** independent firm before go-live; no critical or high findings open.
- **Monitoring:** logs → Loki or CloudWatch (30 days), metrics 13 months, on-call alerts for launches.
