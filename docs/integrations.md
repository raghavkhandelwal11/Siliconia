# Siliconics: Integration Register

| | |
|---|---|
| **Version** | 1.0, 24 Nov 2026 |
| **Related** | TRS v1.2 (D-20 to D-23, sections 10 and 12), LLD v1.1 (sections 4.7, 12, 17) |
| **Purpose** | Every outside service the app depends on: what the free prototype uses today, what production will use, and how to switch each one on. |

> **Rule (TRS D-20):** the prototype uses only free services. Nothing here needs a paid plan, an AI/LLM API key or real money until we decide to go live. Free limits change often; check them again when signing up.

---

## 1. Summary

| # | Service | Used for | Prototype (free, now) | Production candidates (later) | How production is charged | Setting |
|---|---|---|---|---|---|---|
| 1 | Payment gateway | UPI, cards, net banking, refunds, membership auto-renew mandates | `sim-pay` simulator | Razorpay, Cashfree, PayU | % fee per successful payment | `PROVIDER_PAYMENT` |
| 2 | Shipping aggregator | Serviceability, labels, pickups, tracking, COD, returns | `sim-ship` simulator | Shiprocket, Delhivery, iThink Logistics | Per shipment, by weight and zone; COD fee | `PROVIDER_SHIPPING` |
| 3 | Bank payouts | Weekly seller payouts, creator cash-outs | `sim-payout` simulator | RazorpayX, Cashfree Payouts | Per transfer | `PROVIDER_PAYOUT` |
| 4 | KYC checks | PAN verification, bank penny-drop | `sim-kyc` simulator | A licensed KYC API provider | Per check | `PROVIDER_KYC` |
| 5 | SMS | Sign-in OTPs, order updates | `sim-notify` test inbox | MSG91, Gupshup, Twilio | Per SMS; DLT registration needed | `PROVIDER_SMS` |
| 6 | WhatsApp | Order updates, launch alerts | `sim-notify` test inbox | Meta WhatsApp Cloud API, Gupshup | Per conversation or message | `PROVIDER_WHATSAPP` |
| 7 | Email | Receipts, invoices, approvals, alerts | **Brevo free** (about 300 a day) | Amazon SES, or a paid Brevo plan | Per email or monthly plan | `PROVIDER_EMAIL` |
| 8 | Google sign-in | Second sign-in option | **Google OAuth (free)** | Same | Free | `GOOGLE_CLIENT_ID/SECRET` |
| 9 | Object storage | Images, review photos, KYC files, invoices | **Cloudflare R2 free** (10 GB) or MinIO on the VM | Amazon S3 | Per GB stored and requests | `STORAGE_*` |
| 10 | Hosting | Web, API, worker, simulators, database, cache | **Oracle Cloud Always Free VM** (fallback: Vercel + Render + Neon + Upstash free) | AWS Mumbai (ECS, RDS, ElastiCache) | Per hour of compute and storage | Infrastructure files |
| 11 | DNS, HTTPS, CDN, firewall | Address, certificates, caching, attack protection | **Free subdomain (e.g. DuckDNS) + Let's Encrypt via Caddy**; Cloudflare free once a domain exists | CloudFront + AWS WAF (or Cloudflare paid) | Per request/GB; WAF rules | Caddy / DNS config |
| 12 | Domain name | Brand address | None yet (TRS Q-11) | Any registrar | Yearly fee | DNS |
| 13 | Monitoring | Logs, metrics, traces, errors, uptime | **Grafana Cloud free, Sentry free, UptimeRobot free** | Paid Grafana/CloudWatch, Sentry team plan | Per GB / events / seats | `OTEL_*`, `SENTRY_DSN` |
| 14 | CI/CD and images | Tests, builds, deploys | **GitHub Actions + GHCR** (unlimited if public; about 2,000 min/month if private) | Same, paid minutes if needed | Per runner-minute | `.github/workflows` |
| 15 | Malware scanning | Uploaded documents | **ClamAV container** on the VM | ClamAV on AWS Lambda or a scanning service | Compute | `SCAN_ENABLED` |
| 16 | Secrets | Keys and passwords | GitHub encrypted secrets + root-only env file on the VM | AWS Secrets Manager + KMS | Per secret / key | Deploy config |

Not used anywhere: AI/LLM APIs (including Claude), paid analytics, paid search.

---

## 2. How a paid service is switched on

Every row above sits behind an interface in `services/api/internal/provider/<name>/` (LLD section 12). The simulators implement the same interface, so the rest of the code never changes.

1. **Account:** create the business account with the provider and complete its KYC (needs a registered business for payments, payouts and SMS).
2. **Sandbox first:** get test or sandbox keys; never use live keys outside production.
3. **Adapter:** add `internal/provider/<name>/<vendor>/` implementing the interface, including webhook signature checks.
4. **Contract tests:** run the shared adapter contract tests (the same ones the simulator passes) against the sandbox in CI. They must pass before merging.
5. **Prototype trial:** set `PROVIDER_<NAME>=<vendor>` and the secrets on the prototype; run the end-to-end suite and a manual smoke test.
6. **Go-live checklist** (per service below): webhook URL registered, IP allowlists, templates approved, limits and alerts set.
7. **Production:** switch the setting in production. The app refuses to start in production mode if any provider is still `sim` or the test inbox is on (LLD 4.7).

---

## 3. Service details

### 3.1 Payment gateway
- **Interface:** `PaymentProvider` (create order, get status, refund, charge mandate, revoke mandate, verify webhook).
- **Prototype:** `sim-pay` hosted checkout with test UPI IDs and cards (LLD 12). Labelled "TEST MODE – no real money".
- **Go-live needs:** business KYC with the gateway; webhook URL `https://<domain>/api/v1/webhooks/payment`; webhook secret; UPI AutoPay / card mandate enabled for membership renewal; settlement report access for daily reconciliation.
- **Note:** most Indian gateways also offer a free test mode, which can replace `sim-pay` before going live if wanted.

### 3.2 Shipping aggregator
- **Interface:** `ShippingProvider` (serviceability, create shipment, return pickup, cancel, verify webhook).
- **Prototype:** `sim-ship` with seeded PIN codes and timed tracking updates.
- **Go-live needs:** aggregator account with pickup addresses for each seller (or Siliconics' warehouse for launch stock); webhook URL `/api/v1/webhooks/shipping`; COD remittance account; label format check.

### 3.3 Bank payouts
- **Interface:** `PayoutProvider` (transfer, verify webhook).
- **Prototype:** `sim-payout`.
- **Go-live needs:** current account with the payout provider, funded balance, webhook URL `/api/v1/webhooks/payout`, TDS rules confirmed with a CA (TRS Q-05).

### 3.4 KYC checks
- **Interface:** `KYCProvider` (PAN check, penny-drop).
- **Prototype:** `sim-kyc` with test PANs and accounts.
- **Go-live needs:** provider contract and API keys; data-retention terms that match DPDP requirements.

### 3.5 SMS
- **Interface:** `Messenger` (channel `sms`).
- **Prototype:** messages and OTPs go to the test inbox at `/test-inbox` (tester password). Testers read their OTP there (TRS D-21, A-21).
- **Go-live needs:** TRAI DLT registration of the sender ID and every template (OTP, order updates); provider account; delivery-report webhook; spend alerts.

### 3.6 WhatsApp
- **Interface:** `Messenger` (channel `whatsapp`).
- **Prototype:** test inbox.
- **Go-live needs:** WhatsApp Business account and verified number; each template approved by Meta; opt-in recorded per user (`notification_prefs`).

### 3.7 Email
- **Interface:** `Messenger` (channel `email`).
- **Prototype:** Brevo free plan, sending from a verified sender address; `log` adapter locally (emails appear in the test inbox).
- **Go-live needs:** own domain with SPF, DKIM and DMARC; SES production access (or a paid Brevo plan); bounce and complaint handling; List-Unsubscribe on marketing mail.

### 3.8 Google sign-in
- **Prototype and production:** Google Cloud OAuth client (free). Redirect URI `https://<address>/api/v1/auth/google/callback`.
- **Go-live needs:** OAuth consent screen verified with the real domain, privacy policy and terms URLs.

### 3.9 Object storage
- **Interface:** S3 API through `platform/storage`.
- **Prototype:** Cloudflare R2 free tier, or MinIO on the VM if R2 is not used. KYC files are encrypted by the app before upload (LLD 15).
- **Go-live needs:** S3 buckets (`media`, `docs-kyc`, `invoices`, `exports`) with KMS encryption and access logging; CloudFront for public media.

### 3.10 Hosting
- **Prototype:** Oracle Cloud Always Free ARM VM with Docker Compose (LLD 17.1). A card is asked for identity verification but not charged (TRS Q-09). Fallback: Vercel Hobby + Render free + Neon free + Upstash free.
- **Go-live needs:** AWS account in the Mumbai region, Terraform apply of the production modules, load test at 1,500 requests per second (TRS NFR-CAP-02).
- **Note:** Vercel Hobby is for non-commercial use only; it is used just for optional pull-request previews.

### 3.11 DNS, HTTPS, CDN and firewall
- **Prototype:** free subdomain with Let's Encrypt certificates through Caddy; API rate limits always on.
- **When a domain exists:** Cloudflare free plan in front (DNS, CDN, managed firewall rules, one rate-limit rule).
- **Production:** CloudFront + AWS WAF (or Cloudflare paid plan), plus the launch waiting room.

### 3.12 Monitoring
- **Prototype:** Grafana Alloy on the VM → Grafana Cloud free (about 14 days of data); Sentry free for errors; UptimeRobot free outside check every 5 minutes; built-in admin health monitor (TRS FR-HLT).
- **Production:** longer retention (logs 30 days, metrics 13 months) on paid plans.
- **Privacy:** phone numbers, emails, PAN and bank details are masked before anything leaves the VM.

### 3.13 CI/CD
- **Now:** GitHub Actions and GitHub Container Registry. Budget and job layout are in LLD 17.4 and [dev-workflow.md](dev-workflow.md).
- **Decision needed:** public or private repository (TRS Q-10).

---

## 4. Free-tier accounts to create for the prototype

| Account | Needed for | Card asked? |
|---|---|---|
| GitHub | Code, CI, image registry | No |
| Oracle Cloud | Prototype VM | Yes, for verification only (not charged) |
| DuckDNS (or similar) | Free subdomain | No |
| Brevo | Email | No |
| Google Cloud | OAuth client for Google sign-in | No for OAuth only |
| Cloudflare | R2 storage (optional; MinIO instead), later DNS/CDN | R2 may ask for one; MinIO avoids it |
| Grafana Cloud | Monitoring | No |
| Sentry | Error tracking | No |
| UptimeRobot | Outside uptime check | No |
| Vercel (optional) | Pull-request previews | No |

Store every key in GitHub encrypted secrets and the VM's env file, never in the repository. `.env.example` lists every variable with a dummy value.
