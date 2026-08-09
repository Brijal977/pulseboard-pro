# Phase 10 — Observability, Deployment & Production Operations

> **Mission line:** Make PulseBoard production-operable — logging, metrics, tracing, health, deployment, CI/CD — the phase where your sysadmin/SRE background becomes an outright superpower.
> **Placement:** Where software becomes a *service* that runs, is watched, and survives. The differentiator most developers learn late and badly, and that you already half-know from the operator's side.
> **Prerequisite:** Phases 0–9. You have a real, tested, well-architected system to run.

---

## 🧭 The mental-model shift
Juniors think the job ends when the code works on their machine. Operators know that's when the *real* job begins — because software in production fails in ways dev never shows: it gets slow, leaks, falls over at 3am, and someone has to know *why* fast. Elite engineers build software that is *operable*: observable, configurable, health-checkable, and safe to deploy. This is your home turf inverted — you've been on the receiving end of un-operable software; now you build software that respects the operator. That perspective makes senior engineers trust your code instantly.

## 🎯 Mission
Take PulseBoard to production properly: structured logging with correlation IDs, Prometheus metrics, health/readiness endpoints, graceful shutdown, a lean container, a CI pipeline that gates `main`, a real deployment on HTTPS, defined SLOs, and a practiced failure drill. Then monitor real sites — your monitoring tool becomes a thing you keep alive.

## 🧠 What you must command (senior tier)
- **The three pillars of observability:** **logs** (structured, leveled, correlated), **metrics** (counters/gauges/histograms; the RED and USE methods), **traces** (following one request across components). Logging vs monitoring vs observability (can you answer *new* questions about your system without shipping new code?).
- **Structured logging:** JSON logs, log levels, a correlation/request ID threaded through every line of a request, never logging secrets/PII.
- **12-factor config discipline:** config in the environment, not code; secrets management; dev/prod parity; fail-fast on missing config at startup.
- **Containers:** what Docker actually is (namespaces + cgroups — you likely know this), images vs containers, layers, **multi-stage builds** (small prod images), `.dockerignore`, non-root users, Docker Compose for multi-service local dev.
- **CI/CD:** what CI buys (catch breakage on every push), the pipeline (lint → test → build → deploy), GitHub Actions basics, deploy strategies conceptually (rolling, blue-green, canary).
- **Health checks & probes:** liveness vs readiness (you know these from load balancers/k8s — now you *implement* the endpoints), graceful shutdown (drain in-flight requests, finish checks, close the DB, stop timers).
- **Production reliability & SRE vocabulary:** SLIs/SLOs/SLAs and **error budgets**; the **four golden signals** (latency, traffic, errors, saturation); alerting on symptoms users feel, not on causes.
- **Reverse proxy & TLS:** nginx/Caddy in front, TLS termination, security headers — your wheelhouse, now wired to your own app.

## 🏔️ Staff/elite extras
- **Operability as a design property, not an add-on:** the most senior codebases are built to be run — every failure mode is observable, every config is externalized, every deploy is safe and reversible. You design *for* the operator (often future-you at 3am).
- **Incident response as a skill:** detection → mitigation → root cause → **blameless post-mortem** → prevention. Running a "game day" (deliberately breaking prod to test your response) is a staff-level practice straight from the SRE playbook — and your background makes you unusually good at it.
- **SLO-driven engineering:** defining "healthy" *numerically and in advance* so you're not debating it mid-incident; error budgets as the mechanism that balances reliability against shipping speed. This is Google-SRE-book thinking and it's rare in app developers.
- **Alerting hygiene:** alert on symptoms (latency, error rate) not causes (CPU); every alert should be actionable; alert fatigue is a real failure mode. Bad alerting is worse than none.
- **Cost and capacity awareness:** production has a bill and a ceiling; thinking about resource usage and capacity is part of ownership, not an afterthought.

## 📚 Resources
- **Read (free, foundational — *your* territory):** [Google SRE Book](https://sre.google/sre-book/table-of-contents/) — "Embracing Risk," "Service Level Objectives," "Monitoring Distributed Systems." Own this.
- **Read:** [The Twelve-Factor App](https://12factor.net/) (config, logs, disposability) · [RED method (Grafana)](https://grafana.com/blog/2018/08/02/the-red-method-how-to-instrument-your-services/) and the USE method (search "Brendan Gregg USE method").
- **Docs:** [Docker Get Started](https://docs.docker.com/get-started/) + [multi-stage builds](https://docs.docker.com/build/building/multi-stage/) + [Node.js Docker best practices](https://github.com/nodejs/docker-node/blob/main/docs/BestPractices.md) · [GitHub Actions quickstart](https://docs.github.com/en/actions/quickstart).
- **Libraries:** [Pino](https://getpino.io/) (structured logging) · [prom-client](https://github.com/siimon/prom-client) (Prometheus metrics); optionally Prometheus + Grafana via Compose to graph your own metrics — deeply satisfying given your background.

## 🏗️ Tickets
- [ ] **PB-1001 — Structured logging.** Replace `console.log` with Pino: JSON, levels, a request-ID middleware threading a correlation ID through every log line. Never log secrets. **Acceptance:** one request's logs are greppable end-to-end by its correlation ID.
- [ ] **PB-1002 — Config & secrets.** All config from the environment (12-factor); a validated config module that fails fast on startup if a required var is missing. Secrets never in the repo. **Acceptance:** the app refuses to start, with a clear error, when misconfigured.
- [ ] **PB-1003 — Health & graceful shutdown.** `/healthz` (liveness) and `/readyz` (readiness — checks DB). `SIGTERM` drains in-flight requests, finishes running checks, closes the DB, exits cleanly. **Acceptance:** SIGTERM never corrupts state or drops a mid-flight check.
- [ ] **PB-1004 — Metrics.** Expose `/metrics` (Prometheus): checks performed, check-latency histogram, active monitors, open incidents, event-loop lag. **Acceptance:** metrics scrape-able and graphable.
- [ ] **PB-1005 — Dockerize.** Multi-stage Dockerfile → small prod image, non-root user, prod deps only; `.dockerignore`; Compose for app + DB. **Acceptance:** `docker compose up` runs the whole stack; the image is lean.
- [ ] **PB-1006 — CI pipeline.** GitHub Actions on every PR: lint + tests + build the image; block merge on failure. **Acceptance:** a PR that breaks a test cannot be merged.
- [ ] **PB-1007 — Deploy.** VPS with nginx + systemd (your wheelhouse) *or* a PaaS. TLS via Let's Encrypt/Caddy; security headers. Monitor 5 real sites in production. **Acceptance:** PulseBoard is live on HTTPS on the public internet, monitoring real things.
- [ ] **PB-1008 — Define SLOs.** SLIs/SLOs for PulseBoard itself (e.g., "99.5% of API requests < 200ms," "scheduling accuracy within 2s"); wire one real alert. **Acceptance:** `/docs/SLO.md` exists; an alert fires when you break the SLO deliberately.
- [ ] **PB-1009 — Game day.** Deliberately break prod (kill the DB, saturate CPU, point a monitor at a black hole); observe how the system and dashboards respond; write a blameless post-mortem. **Acceptance:** `/docs/incidents/` has one honest post-mortem (what happened / detection / fix / prevention).

## ⚙️ Best practices & professional habits
- Logs are structured and correlated; a request is traceable end-to-end by ID.
- Config and secrets live in the environment; validate at startup and fail fast, loudly, clearly.
- Every service has health endpoints and shuts down gracefully — no dropped requests, no corrupted state on deploy.
- Alert on symptoms users feel; define "healthy" numerically (SLOs) before you're paged.
- Small, non-root, single-purpose images; build once, run anywhere.
- CI on every change is non-negotiable; broken code never reaches `main`.
- Practice failure before it practices on you; write blameless post-mortems.

## ⚠️ Anti-patterns to kill
- `console.log` string soup with no structure or correlation.
- Secrets or config baked into the image/code.
- No health checks; hard shutdowns that drop requests or corrupt state.
- Alerting on CPU instead of on user-facing symptoms; noisy, non-actionable alerts.
- Fat images running as root.
- Deploying without CI; "it passed on my machine."

## 🗣️ How a senior talks about this
"Every log line carries the request's correlation ID, so I can reconstruct one request across services by grepping one value." · "The app fails fast at boot if config's missing — better a clear crash than a mysterious runtime error." · "Readiness checks the DB; liveness just says the process is up — don't conflate them or you'll restart-loop under DB load." · "We alert on p99 latency and error rate, not CPU — symptoms users feel." · "We ran a game day and found our shutdown dropped in-flight checks; fixed and re-tested."

## ✅ Checkpoint (out loud, cold)
1. Logs vs metrics vs traces — what does each answer, and when do you reach for each?
2. Liveness vs readiness — the difference, and what breaks if you confuse them?
3. Walk through PulseBoard's graceful shutdown — every resource released, in order.
4. SLI vs SLO vs SLA with a PulseBoard example of each; what's an error budget for?
5. Why a multi-stage build and a non-root user?
6. The four golden signals — name them and where you'd measure each in PulseBoard.

## 🔗 Connects to
Realizes the reliability patterns from Phase 9 as observable, deployed reality. Depends on the tests from Phase 8 (the CI gate) and secrets thinking from Phase 7. This is where your Phase 0 reproducibility mindset becomes real infrastructure — and where your SRE background pays its biggest dividend.
