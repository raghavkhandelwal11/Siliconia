# Siliconics: Free Services Register

| | |
|---|---|
| **Version** | 2.0, 24 Nov 2026 (replaces `integrations.md`) |
| **Related** | TRS v1.3 (D-20 to D-24, sections 10 and 12), LLD v1.2 (sections 4.7, 12, 17) |
| **Purpose** | Every outside service the current build uses. **All of them are free to use and test.** |

> **Rule (TRS D-20, D-24):** this build uses only free services and simulators. No paid plan, AI/LLM API, MCP server or real money is used. Paid replacements and their plans are kept separately in [`paid-later/`](../paid-later/README.md) and are not part of this build. Free limits change often; check them again when signing up.

---

## 1. Services in this build

| # | Service | Used for | What we use (free) | Limits to watch | Setting |
|---|---|---|---|---|---|
| 1 | Payment gateway | UPI, cards, net banking, refunds, membership auto-renew mandates | `sim-pay` simulator | None (runs on our VM) | `PROVIDER_PAYMENT=sim` |
| 2 | Shipping aggregator | Serviceability, labels, pickups, tracking, COD, returns | `sim-ship` simulator | None | `PROVIDER_SHIPPING=sim` |
| 3 | Bank payouts | Weekly seller payouts, creator cash-outs | `sim-payout` simulator | None | `PROVIDER_PAYOUT=sim` |
| 4 | KYC checks | PAN check, bank penny-drop | `sim-kyc` simulator | None | `PROVIDER_KYC=sim` |
| 5 | SMS | Sign-in OTPs, order updates | `sim-notify` test inbox at `/test-inbox` (tester password) | Messages kept 24 h | `PROVIDER_SMS=sim` |
| 6 | WhatsApp | Order updates, launch alerts | `sim-notify` test inbox | Messages kept 24 h | `PROVIDER_WHATSAPP=sim` |
| 7 | Email | Receipts, invoices, approvals, alerts | **Brevo free** (test inbox locally with `PROVIDER_EMAIL=sim`; needs `BREVO_API_KEY` and a verified `EMAIL_FROM`) | About 300 emails a day | `PROVIDER_EMAIL=brevo` |
| 8 | Google sign-in | Second sign-in option | **Google OAuth** | Free | `GOOGLE_CLIENT_ID/SECRET` |
| 9 | Object storage | Images, review photos, KYC files, invoices | **SeaweedFS** container on the VM (Apache-2.0) or **Cloudflare R2 free** | R2: 10 GB | `STORAGE_*` |
| 10 | Hosting | Web, API, worker, simulators, PostgreSQL, Redis | **Oracle Cloud Always Free VM** (4 ARM cores, 24 GB) | Idle VMs can be reclaimed | Docker Compose files |
| 10b | Hosting fallback (no card) | Same | Vercel Hobby + Render free + Neon free + Upstash free | Render sleeps when idle; Redis command quota | Fallback config (LLD 17.1) |
| 11 | DNS and HTTPS | Address and certificates | **Free subdomain (e.g. DuckDNS) + Let's Encrypt via Caddy** | — | Caddyfile |
| 12 | CDN and firewall | Caching, attack protection | API rate limits always on; **Cloudflare free** once a domain exists (TRS Q-11) | 1 free rate-limit rule | DNS |
| 13 | Monitoring | Logs, metrics, traces, errors, uptime | **Grafana Cloud free, Sentry free, UptimeRobot free** + built-in health monitor | About 14 days of data; Sentry event quota | `OTEL_*`, `SENTRY_DSN` |
| 14 | CI/CD and images | Tests, builds, deploys | **GitHub Actions + GHCR** | Unlimited if public; about 2,000 min/month if private (TRS Q-10) | `.github/workflows` |
| 15 | Malware scanning | Uploaded documents | **ClamAV container** | Memory (1.5 GB) | `SCAN_ENABLED` |
| 16 | Secrets | Keys and passwords | GitHub encrypted secrets + root-only env file on the VM | Manual rotation | Deploy config |
| 17 | Security scanning | Dependencies, secrets, containers, web app | govulncheck, `npm audit`, gitleaks, Trivy, OWASP ZAP (all free) | — | CI |

Not used anywhere: AI/LLM APIs (including Claude), MCP servers, paid analytics, paid search.

---

## 2. Simulators: how to test every "paid" flow for free

All simulators run on our own VM (and locally and in CI) with the same interfaces a real provider would have. Full behaviour is in LLD section 12.

| Simulator | Test inputs |
|---|---|
| `sim-pay` | UPI `success@sim`, `failure@sim`, `pending@sim`, `timeout@sim`; cards `4111 1111 1111 1111` (success), `4000 0000 0000 0002` (decline), `4000 0000 0000 9995` (timeout); net-banking bank list; `mandate-fail@sim` |
| `sim-ship` | Any served Indian PIN code (by India Post circle: metros 2 days, most areas 4 days, hills/islands/north-east 6 days and prepaid only); `999999` → two failed attempts then returned to origin; `999998` → one failed attempt then delivered; `999997` → not served; `999996` → prepaid only. Tracking moves one step every `SIM_SHIP_SPEED` (1 min locally) |
| `sim-payout` | Account `000000000000` fails; others are paid in about 30 s |
| `sim-kyc` | Any well-formed PAN passes except `AAAAA0000A` (not found); bank accounts ending `0000` give a name mismatch and `9999` are closed; names must match at 0.8 or more |
| `sim-notify` | Search by phone number at `/test-inbox` to read OTPs and messages |

Every simulator page shows a **TEST MODE – no real money** banner.

---

## 3. Free accounts to create

| Account | Needed for | Card asked? |
|---|---|---|
| GitHub | Code, CI, image registry | No |
| Oracle Cloud | Prototype VM | Yes, identity check only, not charged (TRS Q-09) |
| DuckDNS (or similar) | Free subdomain | No |
| Brevo | Email | No |
| Google Cloud | OAuth client for Google sign-in | No (OAuth only) |
| Cloudflare | R2 storage (optional; SeaweedFS avoids it), later DNS/CDN | R2 may ask for one; SeaweedFS avoids it |
| Grafana Cloud | Monitoring | No |
| Sentry | Error tracking | No |
| UptimeRobot | Outside uptime check | No |
| Vercel (optional) | Pull-request previews | No |

Store every key in GitHub encrypted secrets and the VM's env file, never in the repository. `.env.example` lists every variable with a dummy value.

---

## 4. Rule for adding anything new

Before adding a new outside service, library or tool, check that it is free for our use. If it is not, write its plan in [`paid-later/`](../paid-later/README.md) instead and do not add it to the build.
