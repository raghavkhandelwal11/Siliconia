# Paid Services Plan (later, not part of the current build)

| | |
|---|---|
| **Version** | 1.0, 24 Nov 2026 |
| **Status** | Plan only. Nothing here is set up or paid for. |
| **Current free setup** | [docs/free-services.md](../docs/free-services.md) |

> Free and paid limits and prices change often. Check every provider's current terms before choosing one.

---

## 1. Paid replacements

| # | Service | Free / simulated now | Production candidates | How it is charged | Setting to change |
|---|---|---|---|---|---|
| 1 | Payment gateway | `sim-pay` | Razorpay, Cashfree, PayU | % fee per successful payment | `PROVIDER_PAYMENT` |
| 2 | Shipping aggregator | `sim-ship` | Shiprocket, Delhivery, iThink Logistics | Per shipment, by weight and zone; COD fee | `PROVIDER_SHIPPING` |
| 3 | Bank payouts | `sim-payout` | RazorpayX, Cashfree Payouts | Per transfer | `PROVIDER_PAYOUT` |
| 4 | KYC checks | `sim-kyc` | A licensed KYC API provider | Per check | `PROVIDER_KYC` |
| 5 | SMS | `sim-notify` test inbox | MSG91, Gupshup, Twilio | Per SMS (DLT registration needed) | `PROVIDER_SMS` |
| 6 | WhatsApp | `sim-notify` test inbox | Meta WhatsApp Cloud API, Gupshup | Per conversation or message | `PROVIDER_WHATSAPP` |
| 7 | Email at scale | Brevo free | Amazon SES or a paid Brevo plan | Per email or monthly plan | `PROVIDER_EMAIL` |
| 8 | Object storage | Cloudflare R2 free / MinIO | Amazon S3 (+ CloudFront for media) | Per GB stored and requests | `STORAGE_*` |
| 9 | Hosting | Oracle Always Free VM | AWS Mumbai (see [production-hosting-plan.md](production-hosting-plan.md)) | Per hour of compute and storage | Infrastructure files |
| 10 | CDN and firewall | Caddy + API rate limits (Cloudflare free once a domain exists) | CloudFront + AWS WAF, or a paid Cloudflare plan | Per request/GB; per rule | DNS / edge config |
| 11 | Domain name | Free subdomain | Any registrar | Yearly fee | DNS |
| 12 | Monitoring | Grafana Cloud, Sentry, UptimeRobot free tiers | Paid Grafana/CloudWatch, Sentry team plan | Per GB / events / seats | `OTEL_*`, `SENTRY_DSN` |
| 13 | Secrets and keys | GitHub secrets + root-only env file | AWS Secrets Manager + KMS | Per secret / key / request | Deploy config |
| 14 | CI minutes | GitHub Actions free minutes | Paid GitHub minutes if needed | Per runner-minute | Billing |
| 15 | Penetration test | OWASP ZAP baseline (free) | Independent security firm | Per engagement | — |
| 16 | Vercel (if web moves there) | Hobby, previews only, non-commercial | Vercel Pro | Per seat and usage | Hosting |

---

## 2. How a paid service is switched on

Every service sits behind an interface in `services/api/internal/provider/<name>/` (LLD section 12). The free simulator implements the same interface, so the rest of the code never changes.

1. **Approve the cost** with the client and record it in the TRS change history.
2. **Account:** create the business account and complete the provider's KYC (payments, payouts and SMS need a registered business).
3. **Sandbox first:** get test or sandbox keys; never use live keys outside production.
4. **Adapter:** add `internal/provider/<name>/<vendor>/` implementing the interface, including webhook signature checks.
5. **Contract tests:** run the shared adapter contract tests (the same ones the simulator passes) against the sandbox in CI. They must pass before merging.
6. **Trial:** set `PROVIDER_<NAME>=<vendor>` and secrets in a staging environment; run the end-to-end suite and a manual smoke test.
7. **Go-live checklist** for that service (section 3), then switch it in production. The app refuses to start in production mode if any provider is still `sim` or the test inbox is on.

---

## 3. Go-live checklists

### 3.1 Payment gateway
- Business KYC with the gateway.
- Webhook URL `https://<domain>/api/v1/webhooks/payment` and webhook secret.
- UPI AutoPay / card mandates enabled for membership auto-renewal; RBI pre-debit notification rules followed (TRS FR-MEM-05).
- Settlement report access for daily reconciliation.
- Decide on debit cards and wallets (TRS Q-04).

### 3.2 Shipping aggregator
- Pickup addresses for each seller, and Siliconics' address for launch stock.
- Webhook URL `/api/v1/webhooks/shipping`; COD remittance account; label format check.

### 3.3 Bank payouts
- Current account with the payout provider and a funded balance.
- Webhook URL `/api/v1/webhooks/payout`.
- TDS rules confirmed with a chartered accountant (TRS Q-05).

### 3.4 KYC checks
- Provider contract and API keys; data-retention terms that match the DPDP Act.

### 3.5 SMS
- TRAI DLT registration of the sender ID and every template (OTP, order updates). This was TRS FR-NOT-04.
- Delivery-report webhook; spend alerts.
- Turn the test inbox off (`FEATURE_TEST_INBOX=false`).

### 3.6 WhatsApp
- WhatsApp Business account and verified number; every template approved by Meta (TRS FR-NOT-04).
- Opt-in recorded per user (`notification_prefs`).

### 3.7 Production email
- Own domain with SPF, DKIM and DMARC; SES production access or a paid Brevo plan.
- Bounce and complaint handling; List-Unsubscribe on marketing mail.

### 3.8 Storage, CDN and firewall
- S3 buckets `media`, `docs-kyc`, `invoices`, `exports` with KMS encryption and access logging; CloudFront for public media.
- AWS WAF managed rules, bot control and the launch waiting-room rule (or the Cloudflare paid equivalent).

### 3.9 Monitoring
- Retention raised to logs 30 days and metrics 13 months.
- On-call alerting for launches.

### 3.10 Google sign-in (free, but needs the real domain)
- OAuth consent screen verified with the real domain, privacy policy and terms URLs.

---

## 4. Legal items that start with the commercial launch

These are legal obligations, not paid tools, but they only apply once real money moves. They are listed in TRS section 14 and must be confirmed by a lawyer and a chartered accountant before go-live: Consumer Protection (E-Commerce) Rules, GST invoices and e-commerce TCS, TDS on payouts, DPDP Act, RBI e-mandate rules, TRAI DLT, Legal Metrology.
