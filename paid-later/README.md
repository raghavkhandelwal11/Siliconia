# paid-later/: NOT part of the current build

> **Stop.** Nothing in this folder is built, configured or paid for now. The current Siliconics build is a **prototype that uses only free services** (TRS decisions D-20 to D-24). Everything that would cost money lives here, so the main `docs/` folder only describes what we are actually building and testing.

Use this folder only when the business decides to go live commercially.

## Contents

| File | What it is |
|---|---|
| [paid-services-plan.md](paid-services-plan.md) | Paid replacements for each simulated or free service (payments, shipping, payouts, KYC, SMS, WhatsApp, production email, storage, monitoring, CDN/WAF, domain), how each is charged, and the go-live checklist and switch-on steps for each |
| [production-hosting-plan.md](production-hosting-plan.md) | Paid production hosting on AWS Mumbai: resources and sizing, launch-day capacity, the client's original production targets (1,000 requests per second, 99.9% uptime, multi-AZ, PITR), production security controls, settings, monitoring retention, load and penetration testing |
| [diagrams/siliconics-production-architecture.png](diagrams/siliconics-production-architecture.png) | Production architecture diagram (image) |
| [diagrams/production-architecture-interactive/](diagrams/production-architecture-interactive/siliconics-hld.html) | Interactive version of the production diagram and its build files. Its links to the detailed LLD diagrams only work from the prototype diagram in `.archify/`. |

## MCP servers and AI services

- The Siliconics app uses **no AI/LLM APIs** (including Claude) and **no MCP servers**.
- No paid MCP server or AI tool is needed to build, run or test it.
- If a paid MCP server or AI service is ever proposed, its plan is added here first and must not be wired into the build until the business approves the cost.

## Rules

1. Code in the main repo may contain **interfaces** (for example `PaymentProvider`) and **free simulators**, but no paid-provider adapters, keys or accounts until a service is switched on from this folder.
2. Switching a service on follows the steps in [paid-services-plan.md](paid-services-plan.md#2-how-a-paid-service-is-switched-on), starting with its sandbox.
3. When a plan here is approved and built, move the relevant section back into `docs/` and note it in the TRS change history.
