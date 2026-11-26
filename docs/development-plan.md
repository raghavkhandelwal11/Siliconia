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
| T0.6 | Docker Compose with Caddy routing, PostgreSQL, Redis and simulator stubs (object storage: SeaweedFS, added in T1.6) | `chore` `area:infra` `P0` `M` |
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
| T1.6 | Seller product editor: specs form, variants, images (uploaded through the API to SeaweedFS/R2), inventory | `feat` `area:web` `area:api` `mod:seller` `P0` `L` |
| T1.7 | Search and listing: full-text + filters + sort; category and search pages | `feat` `mod:catalogue` `P0` `M` |
| T1.8 | Product page (ISR + live block, role-based actions), home page v1 | `feat` `area:web` `mod:catalogue` `P0` `L` |

**Status (25 Nov 2026):** T1.1–T1.3 merged ([Siliconia_Backend#2](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/2), [Siliconia-Frontend#2](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/2)); Google sign-in stays hidden until a free Google OAuth client is configured. T1.4 merged ([Siliconia_Backend#9](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/9), [Siliconia-Frontend#15](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/15)): 12 categories, 53 sub-categories and 239 spec fields seeded; category pages and header menu. Commission and GST per category still wait for Q-01. T1.5 ([Siliconia_Backend#10](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/10), [Siliconia-Frontend#17](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/17)): `sim-kyc`, seller onboarding wizard with PAN, bank and GSTIN checks, and the admin approval queue; document uploads move to T1.6 with object storage. T1.6 ([Siliconia_Backend#11](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/11), [Siliconia-Frontend#19](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/19)): product editor with category spec forms, variants with stock, photos and publishing; SeaweedFS for storage. A UI refresh (themes, motion, showcase home) landed in [Siliconia-Frontend#18](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/18). T1.7 ([Siliconia_Backend#12](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/12), [Siliconia-Frontend#20](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/20)): search and category listings with filters, facets, sort, chips, "Load more" and search suggestions; `make demo` loads a demo store. "Launch deals" and "creator reviewed" filters join with M3 and M4. T1.8 ([Siliconia_Backend#13](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/13), [Siliconia-Frontend#21](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/21)): product page (static part cached 60 s, live price, stock, Electrons and role-based actions, specs, seller, similar products) and home rails (Popular, Local picks, New arrivals). **M1 complete.** Follow-ups: require 3–10 photos to publish (now 1) as the catalogue doc says; "Products of the week" needs the weekly job; launch hero (M3).

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

**Status (26 Nov 2026):** T2.1 merged ([Siliconia_Backend#14](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/14), [Siliconia-Frontend#22](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/22)): cart grouped by seller with stock and price warnings and an Electrons preview, guest cart (device cookie, CSRF-protected) merged on sign-in, wishlist with price drops, add to cart on the product page. Wishlist alerts wait for notifications (T2.7). T2.2 merged ([Siliconia_Backend#15](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/15)): sim-pay (hosted checkout with test fixtures, refunds, mandates, settlement, signed webhooks with retries and duplicates), the payment provider interface, payments/refunds tables and webhook intake with outbox events. T2.3 merged ([Siliconia_Backend#16](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/16), [Siliconia-Frontend#23](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/23)): address book, live checkout quote, orders placed with an Idempotency-Key and stock reserved in the same transaction (one sub-order per seller), COD up to ₹8,000 confirmed at once, online orders handed to sim-pay and held 10 minutes, capture confirms the order, a worker job expires unpaid orders and releases stock, and a payment that arrives after expiry is refunded automatically; checkout, payment result and order list pages, Buy now. For now shipping is free, every PIN is accepted until sim-ship (T2.4), commission is 0 until Q-01 is answered, and spending Electrons waits for M3. T2.4 merged ([Siliconia_Backend#17](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/17), [Siliconia-Frontend#24](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/24)): sim-ship (serviceability with COD and delivery estimates, AWB and label PDF, timed signed tracking webhooks, RTO and failed-attempt test PINs, return pickups), the shipping provider interface, shipments and tracking events moving sub-orders to SHIPPED, OUT_FOR_DELIVERY, DELIVERED (7-day return window, COD collected) or RTO, a seller ship and label API, PIN checks on addresses and checkout, and tracking on the order page. Open point: the LLD (9.3) allows COD for Electra members only; until membership arrives in M3, COD is offered to every buyer within the limit and PIN rules. T2.5 merged ([Siliconia_Backend#18](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/18), [Siliconia-Frontend#25](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/25)): guarded sub-order transitions with a history recorded by a database trigger (actor and note), seller order queue and detail (pack, book courier, label, cancel), buyer order page with tracking, timeline and cancel before shipping (restock and full refund for prepaid), and a GST tax invoice PDF per sub-order from the seller's own series (CGST/SGST or IGST; prototype banner). T2.6 merged ([Siliconia_Backend#19](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/19), [Siliconia-Frontend#26](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/26)): returns and replacements within 7 days of delivery (seller approves, courier pickup, refund to the original method or `REFUND_PENDING` for cash on delivery, zero-value replacement sub-orders), and issues with a 3-step form, a support conversation (admin with authenticator), reopen and auto-close after 7 days. T2.7 merged ([Siliconia_Backend#20](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/20), [Siliconia-Frontend#27](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/27)): outbox events routed to 12 templates; in-app inbox with a live bell (server-sent events over Redis); SMS and WhatsApp to the test inbox; email to the test inbox or Brevo; idempotent per event and channel; channel preferences. **M2 complete.** Follow-ups: photo attachments on returns and issues (buyer uploads); COD refunds wait for payouts (M5); COD for Electra members only once membership exists (M3); wishlist price and stock alerts can now use notifications. Next: M3.

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

**Status (25 Nov 2026):** M3 merged in one PR per repo ([Siliconia_Backend#21](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/21), [Siliconia-Frontend#28](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/28)). T3.1 Electrons ledger (lots per item, pending until the return window closes, hourly maturing, FIFO spend, exact restore and reversal, daily expiry, append-only ledger enforced by a trigger, balances page). T3.2 Electra Membership (money and/or Electrons, auto-renew mandate, pre-debit notice, renewal retries, lapse). T3.3 Electrons slider and COD rules (members only, within the limit, no launch deals, PIN). T3.4 launch admin with the deal-unit calculator and platform-owned launch lots. T3.5 Redis gate + conditional SQL reserve, 10-minute claims, sweeper, live units over SSE, launch page and checkout. T3.6 waiting room with signed admission passes. T3.7 5,000 members racing for 50 units: exactly 50 held, none oversold (runs in CI). Also fixed: launch stock no longer counts as normal stock; early payment webhooks are retried instead of dropped. **M3 complete.** Open: Q-06 GST basis for launch stock; Q-08 returned launch units (kept off sale); launch start alerts; waiting room is always on when a launch sets a rate.

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

**Status (26 Nov 2026):** M4 merged in one PR per repo ([Siliconia_Backend#22](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/22), [Siliconia-Frontend#29](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/29)). T4.1 buyer reviews with photos, helpful votes, reports (three hide a review) and moderation. T4.2 creator application with a follower criterion, admin approval, encrypted PAN/bank KYC, creator studio. T4.3 demo requests shipped through sim-ship, creator reviews ranked by publication, buyer agree/disagree. T4.4 monthly reward pools split by verified agrees × rank weight (LLD 9.4), paid in Electrons. T4.5 `sim-payout` and cash-out (minimum 250, weekly batch, failed transfers give Electrons back). T4.6 promoters with seller-confirmed investments, Promote, Electrons per unit on delivered sales. T4.7 products of the week, bought together, picks for you, feedback to the manufacturer. Fixed: Electrons restores now cover cash-out holds. **M4 complete.** Open: Q-02 pool sizes (set by admins for now); Q-03 promoter rewards (interim: Electrons per unit for every promoter of the product); photo attachments for returns and issues.

## M5 Admin, payouts and health
**Done when:** admins run the whole platform from the console, sellers are paid weekly (simulated) and the health monitor checks the live prototype every 5 minutes.

| ID | Task | Labels |
|---|---|---|
| T5.1 | Seller payouts: weekly batch, commission, statements, verified-tag job | `feat` `mod:seller` `P0` `M` `needs-decision` (Q-01 commission) |
| T5.2 | Admin console: overview, approvals, users, orders/issues/returns, payouts, reviews | `feat` `area:web` `mod:admin` `P0` `L` |
| T5.3 | Admin config, categories, legal documents (re-acceptance), reports (CSV), audit log, ledger adjustments | `feat` `mod:admin` `P0` `M` |
| T5.4 | Synthetic health checks, health dashboard, alerts; Grafana Alloy + Sentry + UptimeRobot | `feat` `mod:health` `P0` `M` |
| T5.5 | Reconciliation, retention purge and ledger invariant jobs | `feat` `area:api` `P1` `S` |

**Status (26 Nov 2026):** M5 merged in one PR per repo ([Siliconia_Backend#23](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/23), [Siliconia-Frontend#30](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/30)). T5.1 weekly payout batches (delivered orders past the return window; platform stock and replacements excluded), commission from item rates, TDS 194-O at 0.1% (`payouts.tds_bps`), admin approve then release through sim-payout (`PO-` refs), failed transfers go into the next batch, seller payouts page with CSV statements, daily verified-tag job (₹4,00,000 over 12 months). T5.2 admin overview, users (search, suspend with session revocation, reactivate), orders and returns, payout batches. T5.3 config editor with version check and history, category commission and GST, legal documents with versions and re-acceptance prompt, CSV reports (formula-escaped), audit log, Electrons adjustments applied by a second admin (also a database check). T5.4 synthetic checks every 5 minutes (API, catalogue, search, product page, simulators; all passing locally), two failures in a row open an incident and alert admins, health page with 30-day uptime; Grafana Alloy, Sentry and UptimeRobot prepared in `deploy/observability` (need your own free accounts). T5.5 daily payment reconciliation against the sim-pay settlement, ledger invariant check, retention purge. **M5 complete.** Open: Q-01 commission (category rates editable by admins meanwhile); COD refunds still need buyer bank details (`REFUND_PENDING`); admin pages are covered by API tests and unit tests, not a browser run (admin sign-in needs an authenticator code).

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

**Status (26 Nov 2026):** merged in [Siliconia_Backend#27](https://github.com/raghavkhandelwal11/Siliconia_Backend/pull/27) and [Siliconia-Frontend#34](https://github.com/raghavkhandelwal11/Siliconia-Frontend/pull/34) (+ #35, #36). **T6.2** nightly workflow: journeys in Chromium, Firefox and WebKit, k6 load test (50 shoppers: p95 511 ms against the 3 s NFR, 0 errors) and an OWASP ZAP baseline that fails on missing security headers; first full run green. **T6.3** Playwright journeys for every role (guest on desktop and phone, buyer checkout with online payment, seller pack and ship, creator and promoter applications, admin with the authenticator). **T6.4** axe WCAG 2.2 AA checks on about 20 pages (fixed: discount badge and green/amber text contrast, an unnamed cart link) and performance budgets from the LLD (JS ≤ 200 KB gzip, LCP < 2.5 s; today ≈185 KB). **T6.5** `make demo` covers all 12 categories, schedules a launch and is safe to re-run; [tester guide](tester-guide.md). Found by load testing and fixed: server-rendered pages shared one per-IP rate-limit bucket (now a server-only internal key). **T6.1** is ready to run once Oracle has free capacity: [go-live runbook](oracle-setup.md), one-command server setup, deploy workflow with automatic rollback (off until the VM exists), nightly backups. **Open:** T6.1 needs the Oracle VM (Q-09) and a DuckDNS name (Q-11); T6.6 UAT with the client.

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
