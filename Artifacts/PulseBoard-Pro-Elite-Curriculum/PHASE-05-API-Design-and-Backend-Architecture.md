# Phase 5 — API Design & Backend Architecture

> **Mission line:** Turn ad-hoc handlers into a deliberately designed API — the public contract of your system — and organize the backend around clean, changeable boundaries.
> **Placement:** Where you stop writing endpoints and start *designing contracts and architecture*. A defining senior skill.
> **Prerequisite:** Phases 0–4. You understand HTTP (Phase 3) and async (Phase 4).

---

## 🧭 The mental-model shift
An endpoint is code. An API is a *promise* — to other developers, other systems, and your future self — that is expensive and painful to break. Juniors grow APIs organically, one handler at a time, and end up with an inconsistent surface nobody can predict. Seniors design the contract *first*, on paper, because changing a doc is cheap and changing a deployed, depended-upon endpoint is not. This phase also introduces the architecture that keeps a backend changeable for years: the dependency rule.

## 🎯 Mission
Design PulseBoard's REST API as a spec before writing it, then implement it behind a clean layered architecture (routes → controllers → services → repositories) with validation, consistent errors, pagination, idempotency, and generated documentation.

## 🧠 What you must command (senior tier)
- **HTTP semantics as contract:** methods and their guarantees (GET safe & idempotent; POST not idempotent; PUT/PATCH/DELETE idempotent), meaningful status codes (200/201/204, 400/401/403/404/409/422, 429, 500/503), the headers that matter.
- **REST properly:** resources as nouns, hierarchy (`/monitors/:id/incidents`), statelessness, no verbs in URLs, and *why* — plus HATEOAS (know it exists, know why few implement it fully).
- **API design decisions:** versioning (URL `/v1/` vs header, tradeoffs), pagination (offset vs **cursor**, and why cursor wins at scale), filtering/sorting conventions, partial responses, and a consistent error shape ([RFC 7807 problem+json](https://www.rfc-editor.org/rfc/rfc7807)).
- **Idempotency in practice:** why DELETE-twice must be safe; **idempotency keys** for POST (the Stripe pattern) so a retried request doesn't double-create — directly relevant to alert-sending later.
- **Validation & contracts:** validate every input at the boundary; schema validation (Zod); never trust the client; 400 (malformed) vs 422 (well-formed but semantically invalid).
- **Architecture — the dependency rule:** dependencies point *inward* toward business logic. Layered/hexagonal (ports & adapters): the domain core knows nothing about Express or the database. Routes handle HTTP, controllers translate, services hold business logic, repositories hold data access. This is the mature form of Phase 1's functional-core/imperative-shell.
- **SOLID and coupling/cohesion** as vocabulary for *why* one design is more changeable than another — especially Single Responsibility and Dependency Inversion. Not dogma; tools for reasoning.
- **REST vs GraphQL vs gRPC/RPC:** the tradeoffs and when each fits. You'll build REST but must defend the choice and know when you'd reach elsewhere.
- **OpenAPI/Swagger:** the description standard — docs that double as contract and can generate clients.

## 🏔️ Staff/elite extras
- **Design docs before code:** for a non-trivial API, write the spec and circulate it (here, to yourself the next morning) to catch flaws on paper. This is a core staff practice — the cheapest place to fix a design is before it exists.
- **API evolution & backward compatibility:** additive changes are safe, removals/renames are breaking; how to deprecate gracefully; why "we'll version it later" is a costly deferral. Thinking in terms of consumers you can't see.
- **Contract-first thinking:** the API shape drives both server and client; shared types (Phase 11) make disagreement a compile error. Treating the boundary as sacred.
- **Errors are part of the contract:** design failure responses as deliberately as success; consistent shape, correlation IDs, never leaking internals. Most APIs are sloppy here — being rigorous marks you.
- **Knowing what a framework does for you:** having built a raw `http` server (your 30-day plan) and now Express, you can articulate exactly what Express adds and what it hides — the mark of someone who *chose* the tool rather than defaulted to it.

## 📚 Resources
- **Read (canonical, free):** [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md) · [Google API Design Guide](https://cloud.google.com/apis/design) — resource-oriented design done right.
- **Study as design (the gold standard):** [Stripe API docs](https://stripe.com/docs/api) — pagination, errors, idempotency. Read it as an exemplar, not to use Stripe. Plus [Stripe: Designing robust APIs with idempotency](https://stripe.com/blog/idempotency).
- **Read:** [RFC 7807 — Problem Details](https://www.rfc-editor.org/rfc/rfc7807) · [Zod docs](https://zod.dev/) · [Express guide](https://expressjs.com/en/guide/routing.html).
- **Architecture:** [Martin Fowler — bliki (Hexagonal, PresentationDomainDataLayering)](https://martinfowler.com/) · revisit *A Philosophy of Software Design* (Ousterhout) on deep vs shallow modules.

## 🏗️ Tickets
- [ ] **PB-501 — Design before code.** In `/docs/API.md`, write the full spec *first*: every endpoint, method, path, request body, response shape, status codes, error format (RFC 7807). Resources: `monitors`, `checks`, `incidents` (users/api-keys come in Phase 7). **Acceptance:** reviewed by you next morning; at least one design flaw caught and fixed on paper.
- [ ] **PB-502 — Layered Express.** Port to Express with routes → controllers → services → repositories. No business logic in route handlers. **Acceptance:** a route handler is < 15 lines and delegates to a service.
- [ ] **PB-503 — Validation layer.** Zod schemas for every body and query param; a validation middleware returns consistent 422s. **Acceptance:** a bad URL or negative interval returns a structured 422, never a 500.
- [ ] **PB-504 — Consistent errors.** Central error middleware maps your Phase-1 typed errors to RFC 7807: `NotFoundError`→404, `ValidationError`→422, unknown→500 with no internal leakage. **Acceptance:** no stack trace ever reaches a client; every error has `type`, `title`, `status`, `detail`.
- [ ] **PB-505 — Pagination & filtering.** `GET /v1/monitors/:id/checks` with cursor pagination and time-range filtering. **Acceptance:** page through 10,000 checks with no offset drift; ADR on why cursor beats offset.
- [ ] **PB-506 — Idempotency keys.** `POST /v1/monitors` honors an `Idempotency-Key` header; a retried request returns the original result without duplicating. **Acceptance:** the same request twice creates one monitor.
- [ ] **PB-507 — Dependency rule enforced.** The domain core imports nothing from Express or the DB; adapters depend on the core, never the reverse. **Acceptance:** you could swap the web framework by rewriting only the adapter layer.
- [ ] **PB-508 — OpenAPI + Swagger UI.** Generate/serve an OpenAPI 3 spec and Swagger UI at `/docs`. **Acceptance:** every endpoint is exercisable from the browser.
- [ ] **PB-509 — ADR-004.** "Why REST (not GraphQL) for PulseBoard, and where GraphQL would have helped." **Acceptance:** demonstrates a choice, not a default.

## ⚙️ Best practices & professional habits
- Design the contract before implementing; a doc is cheap to change, a deployed endpoint is not.
- Validate at the boundary, once, thoroughly; inside the boundary, trust your own types.
- Errors are part of your API — consistent shape everywhere, correlation IDs, zero internal leakage.
- Version from day one, even if it's just `/v1/`.
- Keep GET side-effect-free and idempotent.
- Business logic lives in services, not in HTTP handlers or the database layer.

## ⚠️ Anti-patterns to kill
- Verbs in URLs (`/getMonitors`, `/createMonitor`).
- Business logic stuffed into route handlers.
- Inconsistent, ad-hoc error shapes (sometimes a string, sometimes an object, sometimes a 500 with a stack trace).
- Offset pagination on large, changing datasets (drift and skipped/duplicated rows).
- Leaking stack traces, SQL, or file paths to clients.
- "We'll add versioning later."

## 🗣️ How a senior talks about this
"I wrote the API spec first and caught a resource-modeling flaw before writing a line." · "422 not 400 — the body's well-formed, it's the interval value that's invalid." · "Cursor pagination because offset drifts when rows are inserted mid-scan." · "The domain doesn't import Express; I can swap the transport without touching business logic." · "Idempotency keys here because clients retry, and we can't double-create monitors."

## ✅ Checkpoint (out loud, cold)
1. Semantics of each HTTP method: which are safe, which idempotent, and why it matters for retries.
2. 400 vs 422 vs 409 — a PulseBoard example of each.
3. Why cursor pagination beats offset at scale; what breaks with offset.
4. Explain idempotency keys and why an alert-sending or payment API needs them.
5. State the dependency rule and demonstrate it: how would you swap Express with zero changes to business logic?
6. REST vs GraphQL — one scenario where each is the better choice.

## 🔗 Connects to
The clean architecture here is what makes the database swap (Phase 6) and the reliability patterns (Phase 9) low-risk. The consistent error contract and validation are security controls (Phase 7). The API is what you'll integration-test in Phase 8 and document for your portfolio in Phase 12. Shared types arrive in Phase 11.
