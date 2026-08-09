# Phase 4 — Asynchronous & Concurrent Systems

> **Mission line:** Understand the event loop to the bottom, promises from the inside, streams, backpressure, and worker threads — the single thing that most separates people who "use" JavaScript from people who understand it.
> **Placement:** JavaScript's crux and the beating heart of the PulseBoard engine. Deepens the async material from your 30-day plan into true mastery.
> **Prerequisite:** Phases 0–3. You know closures and how the network behaves.

---

## 🧭 The mental-model shift
Async is where confident-seeming developers quietly guess. They sprinkle `async`/`await`, `Promise.all` the way they've seen others do it, and hope. Elite engineers have a precise model of *when* code runs — the call stack, the queues, the ordering rules — so async behavior is predictable rather than mystical. And they know that "concurrency" in Node is not "parallelism," which changes how you design everything CPU- or I/O-bound.

## 🎯 Mission
Build PulseBoard's live ping engine as a correct, concurrent, resilient daemon: checks run concurrently but bounded, failures never crash the process, timers don't drift or leak, the engine emits events (pub/sub), and heavy work never blocks the loop. This is where PulseBoard becomes a real system.

## 🧠 What you must command (senior tier)
- **The event loop, precisely:** call stack, Web/Node APIs, the **macrotask (task) queue** vs the **microtask (job) queue**, and the rule that *microtasks drain completely between each macrotask*. Predict the output of any mix of sync code, `setTimeout(0)`, and `Promise.resolve().then()`. Know that `queueMicrotask`, promise callbacks, and (in Node) `process.nextTick` are microtask-ish, and `setTimeout`/`setImmediate`/I/O are macrotasks.
- **Node's event loop phases:** timers → pending → poll → check → close, and where `nextTick`/microtasks run relative to them. Why `setImmediate` vs `setTimeout(0)` ordering can differ.
- **Why single-threaded but non-blocking:** the loop offloads I/O to the OS/threadpool (libuv) and processes callbacks when ready — so one slow synchronous function blocks *everything*.
- **Promises from the inside:** states (pending/fulfilled/rejected), `then`/`catch`/`finally`, chaining and error propagation, that a promise is a value representing future completion. Implement a tiny promise to truly get it.
- **async/await mechanics:** an `async` function *always* returns a promise; `await` suspends and resumes on the microtask queue; try/catch around await; the critical distinction between **sequential** awaits (slow, in a loop) and **parallel** (`Promise.all`).
- **Concurrency combinators:** `Promise.all` (all-or-nothing, fails fast), `allSettled` (never rejects, reports each), `race` (first to settle), `any` (first to fulfill) — and exactly when each is correct. Unbounded concurrency as a footgun.
- **Bounded concurrency:** implement `pLimit(n)` yourself — a genuine interview-grade exercise and a real production need (don't ping 10,000 URLs simultaneously).
- **Timers done right:** `setInterval` vs recursive `setTimeout` (the latter is correct for network work — no overlap, no drift when a check runs long), cleanup discipline, no leaked timers.
- **Node fundamentals:** `EventEmitter` (pub/sub), `fs/promises`, and **streams + backpressure** — why streaming matters for large data and what backpressure protects against.
- **Cancellation:** `AbortController`/`AbortSignal` for timeouts and cancelable work — a real-world skill most tutorials skip.

## 🏔️ Staff/elite extras
- **Concurrency vs parallelism, stated crisply:** Node gives you *concurrency* (interleaving many I/O-bound tasks on one thread) but not *parallelism* for JS execution (one thread runs JS). CPU-bound work needs **worker threads** (or child processes/clustering) to actually run in parallel. Knowing which problem you have determines the whole design.
- **Not blocking the loop:** detecting event-loop lag (`perf_hooks.monitorEventLoopDelay`), moving CPU-heavy aggregation to a worker, and why a synchronous `JSON.parse` of a huge payload or a tight compute loop stalls *every* connection.
- **Backpressure as a systems concept:** it recurs in streams, queues (Phase 9), and network flow control (Phase 3) — the general principle of a fast producer not overwhelming a slow consumer.
- **Structured concurrency & resource lifetimes:** always know how an async operation ends (success, error, timeout, cancellation) and that its resources (timers, sockets, file handles) are released on every path. Leaked async is the subtlest class of production bug.
- **The pub/sub decoupling** you'll build (engine emits, logger/alerter subscribe) is the exact architecture behind every monitoring and event-driven system — and the seed of message queues in Phase 9.

## 📚 Resources
- **Watch (again, and closely):** [Philip Roberts — What the heck is the event loop?](https://www.youtube.com/watch?v=8aGhZQkoFbQ) · [Jake Archibald — In The Loop](https://www.youtube.com/watch?v=cCOL7MC4Pl0) (tasks vs microtasks vs rAF). The definitive pair.
- **Read:** [javascript.info — Promises, async/await chapters](https://javascript.info/async) · [MDN — Event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop).
- **Read (Node internals):** [Node.js — The event loop guide](https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick) · [Don't block the event loop](https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop) · [Backpressuring in streams](https://nodejs.org/en/learn/modules/backpressuring-in-streams) · [worker_threads](https://nodejs.org/api/worker_threads.html).
- **Do:** implement a minimal Promise from scratch — follow the [Promises/A+ spec](https://promisesaplus.com/) or a "build your own promise" walkthrough. Nothing teaches promises faster.

## 🏗️ Tickets
- [ ] **PB-401 — Event-loop prediction drill.** `eventloop-experiments.js`: 10 snippets mixing sync, `setTimeout(0)`, `Promise.resolve().then`, and (Node) `process.nextTick`. Predict output *before* running; keep score. **Acceptance:** ≥ 8/10; you can explain the microtask-drain rule.
- [ ] **PB-402 — Build a tiny Promise.** Implement `MyPromise` with `then`/`catch`, resolve/reject, and chaining, passing a handful of your own tests. **Acceptance:** chained `.then`s and error propagation work.
- [ ] **PB-403 — `pingUrl` with cancellation.** Return `{ ok, statusCode, latencyMs, checkedAt, failureKind }` using `fetch` + `AbortController` for timeouts (tie in Phase 3's layered failures). **Acceptance:** a slow endpoint aborts cleanly at the timeout, not a hang.
- [ ] **PB-404 — `delay` and `retry`.** `delay(ms)` as a promise; `retry(fn, attempts)` for async failures (backoff comes in Phase 9). **Acceptance:** retry recovers a flaky function; these two utilities teach most of promise thinking.
- [ ] **PB-405 — `pLimit(n)` from scratch.** Your own bounded-concurrency limiter using only promises. Check all monitors with `Promise.allSettled` under a concurrency cap. **Acceptance:** never more than `n` pings in flight; you can explain why `allSettled` (not `all`) is correct here.
- [ ] **PB-406 — Non-drifting scheduler.** Each monitor checks on its own interval via **recursive `setTimeout`**, with clean pause/resume/stop and zero leaked timers. Run 30 real minutes against 5 URLs. **Acceptance:** a long-running check never overlaps its next run; stopping leaves no live timers.
- [ ] **PB-407 — Engine as EventEmitter.** Emit `check`, `statusChange`, `incidentOpen`, `incidentClose`. A separate `logger.js` subscribes and appends via `fs/promises`. **Acceptance:** the engine has zero knowledge of the logger (pub/sub decoupling).
- [ ] **PB-408 — Don't block the loop.** Introduce a CPU-heavy stats aggregation, measure event-loop lag, then move it to a `worker_thread`. **Acceptance:** loop lag drops measurably; you can explain what was blocking and why a worker fixes it.
- [ ] **PB-409 — Graceful shutdown.** `SIGINT`/`SIGTERM` handler stops timers, finishes in-flight checks, flushes state. **Acceptance:** Ctrl+C never corrupts state or drops a mid-flight check.

## ⚙️ Best practices & professional habits
- Never let one failed check crash the process; isolate and record failures.
- Bound your concurrency; unlimited parallel I/O is a footgun (exhausts sockets, memory, and the target).
- Recursive `setTimeout` for periodic network work — no drift, no overlap.
- Every async operation has a defined end on every path (success/error/timeout/cancel) and releases its resources.
- Emit events instead of calling collaborators directly — decoupling that makes systems extensible.
- Keep CPU-bound work off the event loop.

## ⚠️ Anti-patterns to kill
- `await` in a loop when the calls are independent (accidental sequential slowness).
- `Promise.all` where one failure should not abort the rest (use `allSettled`).
- Unbounded `Promise.all(hugeArray.map(...))` — the classic self-inflicted outage.
- `setInterval` for work that can exceed the interval (overlap and pile-up).
- Fire-and-forget promises with no error handling (unhandled rejections).
- Blocking synchronous work in a request handler or the engine loop.

## 🗣️ How a senior talks about this
"These awaits are independent, so I'll `Promise.all` them — right now they're serialized for no reason." · "`allSettled` because one dead host shouldn't fail the whole sweep." · "We're bounding concurrency to 50 so we don't exhaust sockets or hammer targets." · "That aggregation was blocking the loop and stalling every connection; it's on a worker now." · "The engine emits events; the logger and alerter subscribe — nothing is coupled to the emitter."

## ✅ Checkpoint (out loud, cold)
1. Explain macrotasks vs microtasks and the exact ordering rule. Predict a mixed snippet live.
2. What does an `async` function always return, and what happens to a thrown error inside it?
3. `all` vs `allSettled` vs `race` vs `any` — when is each correct? Why `allSettled` for the monitor sweep?
4. Concurrency vs parallelism in Node — what's the difference and what does it change about handling CPU-bound work?
5. Why recursive `setTimeout` over `setInterval` for checks? What goes wrong with `setInterval` when a check runs long?
6. What blocks the event loop, how do you detect it, and how do you fix it?

## 🔗 Connects to
The pub/sub engine here becomes the alert-queue and message-broker discussion in Phase 9. `retry` grows backoff+jitter and a circuit breaker in Phase 9. Bounded concurrency and non-blocking discipline are load-tested in Phase 9 and observed in Phase 10. Your async utilities are prime unit-test targets in Phase 8.
