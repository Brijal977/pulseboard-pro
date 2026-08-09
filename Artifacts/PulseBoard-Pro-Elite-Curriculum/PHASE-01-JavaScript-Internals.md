# Phase 1 — JavaScript Internals Mastery

> **Mission line:** Take every topic in your Advanced JavaScript cheatsheet and convert it from "read once" into "used in production code and defensible on a whiteboard."
> **Placement:** The deep language spike. This is what lets you say "I know *why* JS behaves this way," not just "I use JS."
> **Prerequisite:** Phase 0. Comfort with basic syntax, functions, arrays.

---

## 🧭 The mental-model shift
Most developers treat the language as a black box that mostly does what they expect until it surprises them — and then they cargo-cult a fix from Stack Overflow. Elite engineers hold an accurate model of *what the engine actually does*, so behavior is derivable, not memorized, and surprises become explicable. This phase builds that model and — crucially — makes you exercise each mechanism in real code (the `/src/core` domain library of PulseBoard), so it sticks.

## 🎯 Mission
Build PulseBoard's pure domain core (`/src/core`) while deliberately touching every internal mechanism: closures for private state, prototypes vs classes, `this` binding, coercion, memory, and typed errors. This is the phase that makes senior engineers nod when you talk.

## 🧠 What you must command (senior tier)

**The engine (V8) — how your code actually runs**
- The pipeline: source → **parser** → **AST** → **interpreter (Ignition)** emits bytecode → hot code → **optimizing compiler (TurboFan)** → optimized machine code. This combo is **JIT compilation**.
- **Hidden classes / shapes:** V8 assigns objects an internal shape based on property layout; objects built with the same properties in the same order share a shape and run faster. Adding/deleting properties dynamically or initializing in different orders causes shape churn and deopts.
- **Inline caching:** call sites remember the shapes they've seen — monomorphic (one shape) is fast, megamorphic (many) is slow.
- **Deoptimization triggers:** changing a variable's type, abusing `arguments`, historically `try/catch` in hot loops, `delete` on hot objects.
- **Memoization:** trading memory for CPU by caching pure-function results — a win when the function is expensive, pure, and repeatedly called; a memory leak when the cache is unbounded.

**Memory — heap, stack, garbage collection**
- **Call stack** holds execution frames; **heap** holds objects. Stack overflow = unbounded recursion (too many frames) — distinct from heap exhaustion.
- **GC:** mark-and-sweep from roots (reachability, not "usedness," is the criterion); generational GC (young/old space, scavenger vs major collections).
- **The classic leak sources:** forgotten globals, uncleared timers/intervals, detached DOM nodes still referenced by JS, closures capturing large scopes, and ever-growing caches/arrays. You will cause and fix one on purpose.

**Execution semantics**
- **Execution context** (global vs function): variable environment, lexical environment, `this` binding. The **creation phase vs execution phase** — which is what "hoisting" *actually is*: `var` → initialized to `undefined`; function declarations fully hoisted; `let`/`const` hoisted but in the **temporal dead zone** until their line runs.
- **Scope chain & lexical environments:** scope is determined by *where code is written*, resolved outward at lookup.
- **Closures:** a function plus its captured lexical environment; the two superpowers — **persistence** (state that survives) and **encapsulation** (state that hides). Know the `var`-loop-`setTimeout` classic cold and why `let` fixes it.
- **`this` — the four binding rules in precedence order:** `new` > explicit (`call`/`apply`/`bind`) > implicit (call-site object) > default (global/`undefined` in strict mode). Arrow functions have **no own `this`** (lexical capture) and no `arguments`. Currying/partial application via `bind`.
- **Types & coercion:** 7 primitives + object; the `==` algorithm well enough to derive `[] == false`; `typeof null === 'object'` and other quirks; value vs reference semantics; `NaN !== NaN` and how to test for it.

**The two pillars & paradigms**
- **Prototypal inheritance:** `__proto__` (the link) vs `prototype` (the property on constructors/classes that becomes the link); the lookup walk; `class` as sugar over prototypes; `Object.create`; `#private` fields.
- **OOP in JS:** factory functions vs constructors vs classes — tradeoffs (prototype method sharing for memory vs closure privacy per instance).
- **Functional programming:** pure functions, referential transparency, idempotence, immutability (and its cost), higher-order functions, partial application vs currying, `pipe`/`compose`, arity; imperative vs declarative as a spectrum.
- **Composition vs inheritance:** the fragile-base-class and gorilla-banana problems; why modern JS favors composition; when a shallow hierarchy is still fine.
- **Modules:** history (IIFE/module pattern → CommonJS → ESM); ESM's gifts (static analysis, tree-shaking, top-level await); live bindings vs CJS value copies; one module = one responsibility as architecture in miniature.
- **Error handling:** `Error` subclassing, `cause` chains (`new Error(msg, { cause })`), operational vs programmer errors (Node doctrine: handle operational, crash on programmer), never swallow errors silently.

## 🏔️ Staff/elite extras
- **Reading the spec-level truth:** know that ECMAScript defines abstract operations (ToPrimitive, ToNumber, SameValueZero) — you don't memorize the spec, but knowing these *exist* lets you resolve any coercion argument definitively.
- **Performance intuition grounded in the engine:** you can now reason about *why* a hot path is slow (megamorphic call site, shape churn, GC pressure) rather than guessing. This is rare even among seniors.
- **Immutability's real cost:** structural sharing vs naive copying; why "just spread everything" can create GC pressure at scale, and when a mutation behind a pure interface is the right call.
- **The `functional core, imperative shell` pattern** — push all I/O, timers, and side effects to the edges; keep the core pure and trivially testable. This single architectural habit pays off in every later phase.

## 📚 Resources
- **Primary:** your own cheatsheet — one section per ticket, now with the editor open.
- **Watch (the two best JS talks ever):** [Philip Roberts — What the heck is the event loop?](https://www.youtube.com/watch?v=8aGhZQkoFbQ) · [Jake Archibald — In The Loop](https://www.youtube.com/watch?v=cCOL7MC4Pl0). Also [Franziska Hinkelmann — JS engines: how do they even?](https://www.youtube.com/watch?v=p-iiEDtpy6I) (V8 from a V8 engineer).
- **Read:** [javascript.info](https://javascript.info/) chapters on closures, garbage collection, prototypal inheritance · [MDN — Memory management](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_management) · [MDN — Equality comparisons and sameness](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness).
- **Read (engine internals):** [V8 blog](https://v8.dev/blog) (Elements kinds) · [Mathias Bynens — Shapes and Inline Caches](https://mathiasbynens.be/notes/shapes-ics).
- **Read (errors):** [Node.js Errors docs](https://nodejs.org/api/errors.html) · [nodebestpractices — Error Handling](https://github.com/goldbergyoni/nodebestpractices#2-error-handling-practices).
- **Book (design judgment):** *A Philosophy of Software Design* — Ousterhout. Thin, profound, on complexity and modules. Read alongside this phase.

## 🏗️ Tickets
- [ ] **PB-101 — Domain models, three ways.** Implement `Monitor` as (a) factory with closure-private state, (b) constructor + prototype methods, (c) `class` with `#private` fields. Write a comparison in `LEARNING.md`: memory, privacy, when you'd choose each. Keep the class. **Acceptance:** all three pass one shared test; comparison written.
- [ ] **PB-102 — The pure core.** `/src/core/stats.js`: `uptimePercent`, `avgLatency`, `p95Latency`, `longestOutage`, `slidingWindow(history, n)`. All pure, immutable (prove with `Object.freeze` in tests). No `for` loops — map/filter/reduce only. Add `pipe()`/`compose()` and build one stat as a pipeline. **Acceptance:** freezing inputs breaks nothing.
- [ ] **PB-103 — Memoization with a limit.** `memoize(fn)` with a `Map`, then `memoizeWithLimit(fn, max)` with LRU eviction. Apply to the most expensive stat. **Acceptance:** test proves the second call skips work; LRU evicts the right key.
- [ ] **PB-104 — Cause a leak, then kill it.** `leaky.js`: a scheduler that forgets to clear intervals + an unbounded history array. Run with `node --inspect`, take two heap snapshots in DevTools, *see* growth, identify retainers, fix it (clear timers; cap history — a teaser for the ring buffer in Phase 2). **Acceptance:** `/docs/memory-leak-postmortem.md` with before/after heap sizes.
- [ ] **PB-105 — `this` gauntlet.** `this-gauntlet.js`: 10 snippets across all four rules + arrows + detached methods + `bind` currying. Predict each output in comments *before* running. **Acceptance:** 10/10 (redo with fresh snippets if not).
- [ ] **PB-106 — Coercion table.** Print `==` vs `===` for the classics (`[] == false`, `'' == 0`, `null == undefined`, `NaN == NaN`…). One paragraph in `LEARNING.md` explaining the ToPrimitive/ToNumber steps. **Acceptance:** you can explain `[] == false` aloud, no notes.
- [ ] **PB-107 — Composition, felt.** Monitor capabilities as composable behaviors: `withRetry`, `withJitter`, `withTags` (object composition/mixins) instead of a `Monitor → HttpMonitor → RetryingHttpMonitor` hierarchy. Write **ADR-002**. **Acceptance:** capabilities stack in any order.
- [ ] **PB-108 — Typed error hierarchy.** `/src/core/errors.js`: `AppError` (code, `isOperational`, `cause`) → `ValidationError`, `NetworkError`, `TimeoutError`, `NotFoundError`. Top-level handler: operational → log & continue; programmer → log & crash. **Acceptance:** a deliberate `TypeError` crashes; a `NetworkError` doesn't.
- [ ] **PB-109 — Hidden-class experiment.** Benchmark summing a property over 1M objects with consistent shape vs random property order. Record in `LEARNING.md`. **Acceptance:** you observe and can explain the difference (or explain why modern V8 minimized it).

## ⚙️ Best practices & professional habits
- Initialize all object properties in the constructor, same order, always — for shape stability *and* readability.
- Default to `const`, immutability, and pure functions in the core; push side effects to the edges (functional core, imperative shell).
- Never use `==` except the idiom `x == null` — and comment it when you do.
- Throw `Error` subclasses, never strings; preserve `cause` when re-throwing.
- Every cache needs an eviction policy. "Unbounded cache" is spelled "memory leak."

## ⚠️ Anti-patterns to kill
- Deep inheritance hierarchies to share behavior (use composition).
- Mutating inputs inside "helper" functions (silent action-at-a-distance bugs).
- Swallowing errors (`catch (e) {}`) — the most expensive four characters in software.
- Sprinkling `this` across arrow/regular functions without knowing which binding applies.

## 🗣️ How a senior talks about this
"That call site went megamorphic once we started passing mixed shapes, so TurboFan bailed out." · "I kept the core pure so it's trivially testable and I can memoize freely." · "It's not that the object is unused — it's still *reachable* through that closure, so GC won't collect it." · "Classes here are just prototype sugar; I chose them for readability, not because I needed anything prototypes can't do."

## ✅ Checkpoint (out loud, cold)
1. Trace what V8 does with a function called 10,000× on the same shape — then what happens on call 10,001 with a different shape.
2. What does a closure close over — values or variables? Prove it with the `var`/`let` loop.
3. All four `this` rules in precedence order, and where arrows fit.
4. How does mark-and-sweep decide what to collect? Name three real leak sources.
5. Why composition over inheritance for monitor capabilities? What breaks with deep hierarchies?

## 🔗 Connects to
The pure core here is what you'll unit-test first in Phase 8. The `functional core / imperative shell` split becomes the dependency rule in Phase 5. The leak you fixed by hand is fixed *properly* with a ring buffer in Phase 2. Your typed errors become RFC-7807 API responses in Phase 5.
