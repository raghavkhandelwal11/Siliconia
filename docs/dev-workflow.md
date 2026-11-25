# Siliconics: Development Workflow

| | |
|---|---|
| **Version** | 1.1, 24 Nov 2026 |
| **Related** | TRS v1.3 (D-22 to D-24, section 12.3), LLD v1.2 (section 17.3), [free-services.md](free-services.md), [paid-later/](../paid-later/README.md) |
| **Goal** | Several people (and several Claude Code sessions) can fix and build things in parallel, with every change tested before it reaches `main`, and `main` always deployable. |

---

## 1. Branches and pull requests

- **Three repositories:** code changes go to `Siliconia_Backend` or `Siliconia-Frontend`; docs and the issue tracker stay in `Siliconia`. A pull request in an app repo closes its task with `Closes raghavkhandelwal11/Siliconia#N`.
- **API changes go backend first:** change `api/openapi.yaml` in the backend, then run `npm run api:sync` in the frontend.

- **Trunk-based:** `main` is protected and always deployable. Every merge to `main` deploys to the prototype automatically.
- **One task → one branch → one pull request.** Branch names: `feat/<short-name>`, `fix/<short-name>`, `chore/<short-name>`, `docs/<short-name>`.
- **Small pull requests:** aim for under about 400 changed lines (generated code excluded). Split big features behind a feature flag.
- **Squash merge** with a Conventional Commit title, e.g. `feat(launch): reserve deal units with Redis gate`.
- **Before merging:** the single required check `ci-success` is green and the branch is up to date with `main` (or the merge queue is used, if the GitHub plan offers it).

## 2. Working in parallel without stepping on each other

| Risk | Rule |
|---|---|
| Two branches edit the same files | Work is split by module folder (`internal/<module>`, `app/(<role>)`). One task touches one module where possible. |
| Parallel local work | Use **git worktrees**: one folder per branch (`git worktree add ../siliconics-<task> -b feat/<task>`). Each Claude Code session runs in its own worktree. |
| Database migrations clash | Files are named by UTC timestamp, e.g. `db/migrations/20261124T101500_add_launch_claims.sql`. Never edit a merged migration; add a new one. Use expand/contract: add the new column or table first, switch the code, remove the old one in a later PR. CI lints migrations. |
| Generated code conflicts | `sqlc` output and the TypeScript API client are regenerated, never hand-merged. On conflict: take either side, rerun `make generate`, commit. CI fails if generated code is out of date. |
| Front end and back end built at the same time | Change `api/openapi.yaml` first in its own small PR. Both sides then build against the merged contract; the web app uses mocked responses from the spec until the endpoint exists. |
| Unfinished features | Merge behind a feature flag (off by default) instead of keeping long-lived branches. |
| Shared test data | Tests create their own data with unique IDs and clean up; nothing depends on test order. |

## 3. Continuous integration

Every push to a pull request runs `ci.yml`. Jobs run **in parallel**; outdated runs are cancelled automatically when a newer commit arrives:

| Job | What it checks | Typical time |
|---|---|---|
| `lint-go`, `lint-web` | golangci-lint; ESLint, Prettier, TypeScript | 1–2 min |
| `unit-go`, `unit-web` | Go tests with the race detector (80% coverage on core modules); Vitest | 2–4 min |
| `integration` | API against real PostgreSQL 16 and Redis 7 containers plus simulators | 4–6 min |
| `contract` | OpenAPI lint, `sqlc diff`, generated client up to date, migration lint | 1 min |
| `e2e` | Full Docker Compose stack; Playwright split into 3 shards (Chromium) | 6–8 min |
| `security` | govulncheck, `npm audit`, gitleaks (secrets), Trivy | 1–2 min |
| `build` | All images build | 3–4 min |
| `ci-success` | Waits for everything above; the only required check (each app repo has its own `ci.yml`) | — |

Path filters skip jobs whose code did not change (a docs-only PR runs in about a minute).

**Other workflows:** `deploy.yml` (push to `main` → prototype, automatic rollback), `nightly.yml` (all browsers, load test, 5,000-buyer oversell test, ZAP scan), `deploy-sha` (manual rollback to any earlier build).

**Run CI locally before pushing:**

```bash
make check
```

`make check` runs the same lint, unit, contract and integration steps as CI; `make e2e` starts the Compose stack and runs Playwright.

Local data: `make up` starts the stack (Caddy on http://localhost:8080) and seeds settings and the 12 categories. `make demo` adds a one-time demo store with 23 products and generated photos (fictional brands) so listings and search have something to show. `make admin PHONE=… NAME=…` creates an admin (at most 3). Sign-in codes appear in the test inbox at `/test-inbox`.

**Free-minute budget:** a private repository gets about 2,000 Linux minutes a month (TRS Q-10). Keep jobs cached and filtered; heavy suites run nightly on weekdays only. A public repository removes the limit.

## 4. Tests to add with each change

| Change | Tests required |
|---|---|
| Business rule (Electrons, launch units, COD, pool split, payouts) | Unit tests including every worked example from TRS section 7 |
| Database or provider behaviour | Integration test against real PostgreSQL/Redis and the simulator |
| New or changed user journey | Playwright end-to-end test |
| API endpoint | OpenAPI updated; contract test; authorization test for every role |
| Bug fix | A test that fails before the fix |

Tests must be deterministic: fake clock for time-based rules, seeded data, simulator fixtures (`success@sim`, test PANs, test PIN codes). A flaky test is labelled `flaky`, moved to a quarantine job, and fixed within two working days.

## 5. Deploying and rolling back

- Merge to `main` → images built and pushed to GHCR → deployed to the prototype VM → migrations → smoke and synthetic checks → automatic rollback on failure (LLD 17.1).
- Manual rollback: run **deploy-sha** with an earlier commit SHA. Migrations are expand/contract, so the previous version keeps working.
- The admin health monitor and UptimeRobot alert on failures after deploy.

## 6. Secrets and data

- No secrets in the repository; gitleaks blocks them. Keys live in GitHub encrypted secrets and the VM's root-only env file. `.env.example` lists every variable with dummy values.
- No real personal data in CI, local or prototype: seed data and synthetic accounts only.
- Only free services and simulators are used ([free-services.md](free-services.md)). No paid provider keys, accounts or adapters are added; paid plans stay in [paid-later/](../paid-later/README.md) until the client approves them.

## 7. Definition of done

A task is done when:
1. The code, tests and docs (OpenAPI, LLD if the design changed) are in one pull request.
2. `ci-success` is green.
3. The change is reviewed (another person, or a Claude Code review pass).
4. It is merged, deployed to the prototype, and the post-deploy checks passed.

## 8. One-time repository setup (before the first feature)

- [ ] Add a `.gitignore` (at least `.DS_Store`, `node_modules/`, `.env`, build output, and Archify review folders such as `.archify/**/review-*` and `*.visual-check.*`).
- [ ] Protect `main` in GitHub settings: require a pull request, require the `ci-success` check, require branches to be up to date, block force-pushes and deletion.
- [ ] Turn on Dependabot (or Renovate) for Go, npm, Docker and GitHub Actions updates.
- [ ] Add the repository secrets used by `deploy.yml` (VM host, SSH deploy key, env file values).
- [ ] Add `CODEOWNERS` if specific people should review specific modules.
- [ ] Add a pull request template with the Definition of done checklist.
