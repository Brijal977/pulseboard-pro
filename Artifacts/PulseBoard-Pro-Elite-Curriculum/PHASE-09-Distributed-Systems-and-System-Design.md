# Phase 9 — Distributed Systems & System Design

> **Mission line:** Zoom out from code to *systems*: reliability patterns, scaling, queues, consistency, and the ability to whiteboard a design like a staff engineer.
> **Placement:** Where "make good architectural decisions" fully lands. The phase that most directly earns the senior title in interviews and on the job.
> **Prerequisite:** Phases 0–8. You have a real, tested system to reason about and stress.

---

## 🧭 The mental-model shift
Juniors reason about one process on one machine on a good day. Seniors reason about *many* processes, across a network that will fail, under load that will grow, where partial failure is the normal case and "it worked in dev" means nothing. The defining move is designing for failure *first* — assuming every network call times out, every dependency dies, every retry storms — and building systems that degrade gracefully instead of collapsing. System design is applied judgment under uncertainty, which is the essence of seniority.

## 🎯 Mission
Refactor PulseBoard around clean boundaries and reliability patterns (circuit breaker, backoff+jitter, queues, no blocked loop), then produce a full design document and be able to whiteboard "design a system like PulseBoard at 100× scale" cold.

## 🧠 What you must command (senior tier)
- **Architectural boundaries:** the dependency rule (dependencies point inward toward business logic), ports & adapters / hexagonal architecture — the mature form of Phase 1's functional core / imperative shell.
- **SOLID, coupling & cohesion** as the vocabulary for *why* one design changes more easily than another — especially Single Responsibility and Dependency Inversion.
- **Design patterns that actually recur:** pub/sub (built in Phase 4), strategy, factory, adapter, repository, dependency injection, **circuit breaker** (stop hammering a down dependency), bulkhead (isolate failures). Recognizing them in the wild, not memorizing the GoF catalog.
- **Reliability patterns:** timeouts everywhere, **retries with exponential backoff + jitter**, circuit breakers, idempotency (again — retries need it), graceful degradation, failing fast vs failing safe.
- **Concurrency at the system level:** what blocks the Node loop and the fixes (worker threads, offloading, streaming); backpressure across process boundaries.
- **System design vocabulary:** vertical vs horizontal scaling, statelessness as the enabler of horizontal scale, load balancing, caching layers (and invalidation), **queues** for decoupling and load-leveling, the CAP theorem at a conversational level, eventual consistency and when it's acceptable.
- **Message queues:** why alert-sending belongs behind a queue (decoupling, retry, load-leveling, not losing alerts if the sender is down), at-least-once vs at-most-once vs exactly-once (and why exactly-once is mostly a myth you approximate with idempotency).
- **Back-of-the-envelope estimation:** checks/sec, storage/day, connections — sizing a design before building it.

## 🏔️ Staff/elite extras
- **Designing for failure as the default posture:** everything across a network fails; the interesting question is always "what happens when this dependency is down/slow/flaky?" Staff engineers ask it reflexively, before the happy path.
- **Identifying where a system breaks *first* under load:** the scarce skill isn't "make it scale," it's knowing *which* component (the single-node scheduler? DB writes? SSE connections? the queue?) is the bottleneck, and the specific fix for each. This is the meat of a system-design interview and of real capacity planning.
- **Consistency tradeoffs with intent:** choosing eventual consistency *deliberately* where it's acceptable (dashboard freshness) and strong consistency where it isn't (billing, auth), and being able to defend the line.
- **The cost of distribution:** distributed systems are *harder* — network partitions, clock skew, partial failure, debugging across machines. Knowing when *not* to distribute (a monolith is often the right call) is as senior as knowing how to.
- **Simplicity as a senior value:** the best design is the simplest one that meets the real requirements and failure modes — not the most impressive. Ousterhout's "deep modules, simple interfaces" and the discipline to *not* over-engineer.

## 📚 Resources
- **Read (the best free system-design resource):** [System Design Primer](https://github.com/donnemartin/system-design-primer) — fundamentals + at least one worked example.
- **Book (the bible, return to for years):** *Designing Data-Intensive Applications* — Kleppmann. Read Ch. 1 (reliability/scalability/maintainability), Ch. 5 (replication); skim 8–9 (distributed troubles). Even three chapters rewires how you think.
- **Read:** [The Twelve-Factor App](https://12factor.net/) · [Martin Fowler — CircuitBreaker](https://martinfowler.com/bliki/CircuitBreaker.html) · [AWS Builders' Library — Timeouts, retries, and backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/) (written by people running this at planetary scale).
- **Watch:** ByteByteGo / Alex Xu and Hussein Nasser on YouTube for system-design intuition.
- **Book (interview vocabulary):** *System Design Interview* — Alex Xu, Vol. 1. Very readable; teaches the language even if you're not interviewing yet.
- **Docs:** [Node.js worker_threads](https://nodejs.org/api/worker_threads.html).

## 🏗️ Tickets
- [ ] **PB-901 — Enforce the dependency rule.** `/src/core` imports nothing from Express/DB/HTTP; adapters depend on core, never the reverse. **Acceptance:** you could swap SQLite for Postgres by writing a new repository adapter, touching zero core files.
- [ ] **PB-902 — Circuit breaker.** Wrap the engine so a repeatedly-failing monitor opens its circuit (checked less often) with half-open probes to recover. **Acceptance:** a permanently-down monitor backs off; you can explain all three states.
- [ ] **PB-903 — Backoff + jitter.** Upgrade Phase-4 `retry` to exponential backoff *with jitter*; explain in `LEARNING.md` why jitter prevents thundering-herd/retry storms. **Acceptance:** retries spread out; you can explain jitter's purpose.
- [ ] **PB-904 — Alert queue.** Alert-sending (email/webhook) behind an in-process queue with retry and a dead-letter list for permanent failures. **Acceptance:** if the sender is down, alerts queue and retry rather than being lost.
- [ ] **PB-905 — Don't block the loop (systems view).** Move heavy aggregation to a worker; measure event-loop lag before/after (`perf_hooks.monitorEventLoopDelay`). **Acceptance:** loop lag drops; you can explain what was blocking.
- [ ] **PB-906 — The design doc.** `/docs/DESIGN.md`: components, data flow, the scaling story ("to handle 100k monitors I would…"), failure modes and mitigations, back-of-envelope capacity math. **Acceptance:** a stranger could understand the architecture from this alone.
- [ ] **PB-907 — Whiteboard rehearsal.** Alone, out loud, design "a monitoring system like PulseBoard" from scratch in 30 minutes as if interviewing. Record it; watch it; note every hand-wave. **Acceptance:** a clean second take.
- [ ] **PB-908 — ADR-007: scale to 100k monitors.** Where the current design breaks *first* (single-node scheduler? DB write throughput? SSE connection count?) and the specific fix for each. This ADR *is* a system-design interview answer. **Acceptance:** names the first bottleneck and its fix, in order.

## ⚙️ Best practices & professional habits
- Dependencies point inward; business logic must not know how it's delivered or stored.
- Design for failure: everything across the network fails — timeout, retry with backoff+jitter, break the circuit, degrade gracefully.
- Prefer statelessness in request handling; push state to DB/cache to scale horizontally.
- Decouple with queues where losing a message is unacceptable.
- Never block the event loop.
- Measure before scaling; know your numbers.
- Choose the simplest design that meets the real failure modes — resist over-engineering.

## ⚠️ Anti-patterns to kill
- Happy-path-only design with no failure story.
- Retries with no backoff or jitter (self-inflicted DDoS).
- Hammering a down dependency every interval (no circuit breaker).
- Fire-and-forget alerts that vanish if the sender is down.
- Business logic coupled to the framework or the database.
- Distributing for its own sake / resume-driven architecture.

## 🗣️ How a senior talks about this
"What happens when the alert sender is down? Right now we lose alerts — that's why it goes behind a queue with a dead-letter." · "Retries need jitter or every client retries in lockstep and stampedes the recovering service." · "At 100k monitors the single-node scheduler is the first thing to break; I'd shard by monitor-id across workers." · "This can be eventually consistent — dashboard freshness within a few seconds is fine — so I won't pay for strong consistency here." · "A monolith is correct here; distributing would add partition and clock problems we don't need."

## ✅ Checkpoint (out loud, cold)
1. State the dependency rule and demonstrate swapping the database with zero business-logic changes.
2. Walk through the three circuit-breaker states and why the pattern exists.
3. Why does retry need jitter? What's the failure mode without it?
4. When do you add a message queue, and what does it buy you?
5. Whiteboard: monitor 100,000 URLs every minute — where does a single-node design break first, and what's your fix?
6. Give one place eventual consistency is fine in PulseBoard and one place it isn't.

## 🔗 Connects to
Extends the pub/sub engine (Phase 4), the clean architecture (Phase 5), and the caching/consistency ideas (Phase 6). The reliability patterns are load-tested and observed in Phase 10. The design doc and whiteboard reps are central to your capstone and interview readiness in Phase 12.
