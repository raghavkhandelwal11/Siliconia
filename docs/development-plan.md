# Siliconics: Development Plan

| | |
|---|---|
| **Version** | 1.1, 24 Nov 2026 |
| **Based on** | TRS v1.3, LLD v1.2, [product-catalogue-and-ui-features.md](product-catalogue-and-ui-features.md), [dev-workflow.md](dev-workflow.md) |
| **Scope** | The free prototype only. Nothing here uses a paid service (see [`paid-later/`](../paid-later/README.md)). |

**Repositories:** docs and issues in [Siliconia](https://github.com/raghavkhandelwal11/Siliconia); code in [Siliconia_Backend](https://github.com/raghavkhandelwal11/Siliconia_Backend) and [Siliconia-Frontend](https://github.com/raghavkhandelwal11/Siliconia-Frontend) (LLD section 3).

**7 milestones (M0–M6).** Each one ends with a working demo on the prototype. One task = one branch = one pull request. Tasks without arrows between them can run in parallel.

---

## Labels

| Group | Labels | Use |
|---|---|---|
| Type | `feat` · `bug` · `chore` · `test` · `docs` | What kind of change |
| Area | `area:web` · `area:api` · `area:db` · `area:infra` · `area:sim` · `area:design` | Where the code lives |
| Module | `mod:auth` · `mod:catalogue` · `mod:checkout` · `mod:orders` · `mod:launch` · `mod:electrons` · `mod:membership` · `mod:reviews` · `mod:programmes` (creators and promoters) · `mod:seller` · `mod:admin` · `mod:notify` · `mod:health` | Which part of the product |
| Priority | `P0` must · `P1` should · `P2` could | Matches TRS Must / Should / Could |
| Size | `S` (≤ 1 day) · `M` (2–3 days) · `L` (4–5 days, split if bigger) | Rough effort |
| Flags | `needs-decision` · `blocked` · `good-first-task` | Waiting on the client, waiting on another task, easy start |

GitHub milestones use the names below (`M0 Foundation`, …).

---

## M0 Foundation
**Status (24 Nov 2026):** built and tested locally; in review as [Siliconia_Backend#1](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/1) and [Siliconia-Frontend#1](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/1). Deferred: object storage to T1.6.

**Done when:** `docker compose up` runs the whole stack locally, every pull request runs CI, and a "hello" page and `/api/v1/healthz` respond.

| ID | Task | Labels |
|---|---|---|
| T0.1 | Repository layouts for backend and frontend (LLD section 3), `.gitignore`, `.env.example`, `Makefile` | `chore` `area:infra` `P0` `S` |
| T0.2 | Go API skeleton: chi router, config, structured logs, middleware chain, RFC 9457 errors, health probes | `feat` `area:api` `P0` `M` |
| T0.3 | PostgreSQL migrations tool, sqlc setup, seed command, outbox + idempotency tables | `feat` `area:db` `P0` `M` |
| T0.4 | Redis client, rate limiter, asynq worker skeleton with job locks | `feat` `area:api` `P0` `M` |
| T0.5 | Next.js app skeleton: route groups, app shell (header, footer, TEST MODE banner), design tokens, next-intl, generated API client from `openapi.yaml` | `feat` `area:web` `area:design` `P0` `L` |
| T0.6 | Docker Compose with Caddy routing, PostgreSQL, Redis, MinIO and simulator stubs | `chore` `area:infra` `P0` `M` |
| T0.7 | GitHub Actions `ci.yml` (parallel lint, unit, integration, contract, build, security → `ci-success`), branch protection, Dependabot | `chore` `area:infra` `P0` `M` |
| T0.8 | `sim-notify` test inbox (store + search by phone, tester password) | `feat` `area:sim` `mod:notify` `P0` `S` |

## M1 Accounts and catalogue
**Done when:** people sign up by phone OTP (read from the test inbox) or Google, sellers apply and get approved, list products, and anyone can browse, search and open product pages.

| ID | Task | Labels |
|---|---|---|
| T1.1 | Accounts, roles (one per account), sessions, refresh rotation, max-3-admin trigger | `feat` `area:api` `area:db` `mod:auth` `P0` `L` |
| T1.2 | Phone OTP sign-in/up and Google sign-in; choose-account; phone verify for Google accounts | `feat` `area:api` `area:web` `mod:auth` `P0` `L` |
| T1.3 | Admin TOTP and role switch (acting-as banner, audit log) | `feat` `mod:auth` `mod:admin` `P0` `M` |
| T1.4 | Categories with spec schemas; seed the 12 categories | `feat` `area:db` `mod:catalogue` `P0` `M` |
| T1.5 | `sim-kyc` simulator; seller onboarding wizard and approval queue (basic admin page) | `feat` `area:sim` `mod:seller` `P0` `L` |
| T1.6 | Seller product editor: specs form, variants, images (presigned upload), inventory | `feat` `area:web` `area:api` `mod:seller` `P0` `L` |
| T1.7 | Search and listing: full-text + filters + sort; category and search pages | `feat` `mod:catalogue` `P0` `M` |
| T1.8 | Product page (ISR + live block, role-based actions), home page v1 | `feat` `area:web` `mod:catalogue` `P0` `L` |

**Status (25 Nov 2026):** T1.1–T1.3 merged ([Siliconia_Backend#2](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/2), [Siliconia-Frontend#2](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/2)); Google sign-in stays hidden until a free Google OAuth client is configured. T1.4 merged ([Siliconia_Backend#9](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/9), [Siliconia-Frontend#15](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/15)): 12 categories, 53 sub-categories and 239 spec fields seeded; category pages and header menu. Commission and GST per category still wait for Q-01. Next: T1.5.

## M2 Cart, checkout and orders
**Done when:** a buyer pays through sim-pay (or COD/Electrons stubs), the seller ships through sim-ship, the buyer tracks, returns or raises an issue, and emails and test-inbox messages are sent.

| ID | Task | Labels |
|---|---|---|
| T2.1 | Cart and wishlist (guest cart, merge on sign-in) | `feat` `mod:checkout` `P0` `M` |
| T2.2 | `sim-pay`: hosted checkout page, signed webhooks, refunds, mandates, settlement report | `feat` `area:sim` `mod:checkout` `P0` `L` |
| T2.3 | Checkout: quote, place order (stock reservation, idempotency), payment hand-off, result page, expiry sweeper | `feat` `mod:checkout` `P0` `L` |
| T2.4 | `sim-ship`: serviceability, AWB + label, timed tracking webhooks, RTO, returns pickups | `feat` `area:sim` `mod:orders` `P0` `M` |
| T2.5 | Orders: sub-order state machine, seller pack/ship, buyer timeline, cancel, GST invoice PDF | `feat` `mod:orders` `P0` `L` |
| T2.6 | Returns/replacements (7 days) and issues (3-step, chat) | `feat` `mod:orders` `P0` `M` |
| T2.7 | Notifications: event routing, Brevo email adapter, SMS/WhatsApp to test inbox, in-app bell (SSE), preferences | `feat` `mod:notify` `P0` `M` |

## M3 Membership, Electrons and launch deals
**Done when:** a buyer becomes an Electra Member, earns and spends Electrons, and members buy launch-deal units without overselling, including the waiting room.

| ID | Task | Labels |
|---|---|---|
| T3.1 | Electrons ledger: lots, earn on paid orders, maturing, FIFO spend, expiry, reversal, balances page | `feat` `mod:electrons` `P0` `L` |
| T3.2 | Electra Membership: purchase (money/Electrons), auto-renew mandate, renewal job, membership page | `feat` `mod:membership` `P0` `M` |
| T3.3 | COD rules and Electrons at checkout (slider, eligibility reasons) | `feat` `mod:checkout` `P0` `S` |
| T3.4 | Launch admin: deal-unit calculator, launch stock as platform-owned inventory, scheduling | `feat` `mod:launch` `mod:admin` `P0` `M` `needs-decision` (Q-06 GST basis) |
| T3.5 | Launch reserve: Redis gate + conditional SQL, claims, hold sweeper, SSE units left, launch page | `feat` `mod:launch` `P0` `L` |
| T3.6 | Waiting room (queue, admission tokens) | `feat` `mod:launch` `P1` `M` |
| T3.7 | Concurrency test: 5,000 buyers, zero oversell | `test` `mod:launch` `P0` `S` |

## M4 Reviews, creators, promoters and discovery
**Done when:** buyers review and vote, creators apply, get demos, post reviews and cash out (simulated), promoters promote products, and the home page shows products of the week and recommendations.

| ID | Task | Labels |
|---|---|---|
| T4.1 | Buyer reviews with photos, helpful votes, moderation reports | `feat` `mod:reviews` `P0` `M` |
| T4.2 | Creator onboarding, criteria check, approval; creator portal | `feat` `mod:programmes` `P0` `M` |
| T4.3 | Demo requests (seller approval, sim-ship), creator reviews with video links, buyer agree/disagree | `feat` `mod:programmes` `mod:reviews` `P0` `M` |
| T4.4 | Reward pool settlement (monthly), creator earnings | `feat` `mod:programmes` `mod:electrons` `P0` `M` `needs-decision` (Q-02 pool size) |
| T4.5 | `sim-payout` and creator cash-out (KYC, min 250, weekly batch) | `feat` `area:sim` `mod:programmes` `P0` `M` |
| T4.6 | Promoter onboarding, seller confirmation, Promote button, promoter rewards | `feat` `mod:programmes` `P0` `M` `needs-decision` (Q-03) |
| T4.7 | Products of the week, recommendations, similar products, feedback to manufacturer | `feat` `mod:catalogue` `P1` `M` |

## M5 Admin, payouts and health
**Done when:** admins run the whole platform from the console, sellers are paid weekly (simulated) and the health monitor checks the live prototype every 5 minutes.

| ID | Task | Labels |
|---|---|---|
| T5.1 | Seller payouts: weekly batch, commission, statements, verified-tag job | `feat` `mod:seller` `P0` `M` `needs-decision` (Q-01 commission) |
| T5.2 | Admin console: overview, approvals, users, orders/issues/returns, payouts, reviews | `feat` `area:web` `mod:admin` `P0` `L` |
| T5.3 | Admin config, categories, legal documents (re-acceptance), reports (CSV), audit log, ledger adjustments | `feat` `mod:admin` `P0` `M` |
| T5.4 | Synthetic health checks, health dashboard, alerts; Grafana Alloy + Sentry + UptimeRobot | `feat` `mod:health` `P0` `M` |
| T5.5 | Reconciliation, retention purge and ledger invariant jobs | `feat` `area:api` `P1` `S` |

## M6 Prototype go-live (free hosting)
**Done when:** the prototype runs on the free VM with automatic deploys and rollback, passes load, security and accessibility checks, and the client signs off the BRD checklist.

| ID | Task | Labels |
|---|---|---|
| T6.1 | Oracle free VM bootstrap, Caddy + free subdomain, `deploy.yml` with rollback, nightly backups | `chore` `area:infra` `P0` `M` `needs-decision` (Q-09 card, Q-11 domain) |
| T6.2 | `nightly.yml`: browser matrix, k6 load test, ZAP baseline | `test` `area:infra` `P0` `S` |
| T6.3 | Playwright journeys for every role (from the page-flow diagrams) | `test` `area:web` `P0` `M` |
| T6.4 | Accessibility pass (WCAG 2.2 AA, colour-blind checks) and performance budgets | `test` `area:web` `area:design` `P0` `M` |
| T6.5 | Demo data seed (sellers, products in all 12 categories, a scheduled launch) and tester guide | `chore` `docs` `P0` `S` |
| T6.6 | UAT with the client against the BRD traceability table; fix list | `test` `docs` `P0` `M` |

> **Tip:** T6.1 can be done during M0–M1 as soon as Q-09 and Q-11 are answered, so every merge reaches the prototype early. Until then, merges are tested in CI only.

---

## Order and parallel work

```
M0 ──► M1 ──► M2 ──► M3 ──► M5 ──► M6
              └────► M4 ──────┘
```

- **Inside M0:** T0.1 first, then T0.2–T0.8 in parallel.
- **M1:** T1.1 → T1.2/T1.3; T1.4 → T1.6/T1.7 → T1.8; T1.5 in parallel.
- **M2:** T2.1, T2.2, T2.4 in parallel → T2.3 → T2.5 → T2.6; T2.7 any time.
- **M3 and M4** can run side by side after M2 (separate modules, one shared dependency: T3.1 Electrons before T4.4 and T4.5).
- **M5** needs M3 and M4. **M6** test work (T6.2–T6.4) can start alongside M5.

## Decisions needed (from TRS section 15.2)

| Needed for | Question |
|---|---|
| T3.4 | Q-06: launch 4% profit with or without GST? |
| T3.4 | Q-08: who keeps returned launch units? |
| T4.4 | Q-02: who funds the creator pool, and how big? |
| T4.5, T4.6 | Q-03: can promoters cash out? |
| T5.1 | Q-01: commission % per category |
| T2.2 | Q-04: debit cards and wallets? |
| T6.1 | Q-09: card for Oracle verification; Q-11: domain |
| CI budget | Q-10: public or private repo |
