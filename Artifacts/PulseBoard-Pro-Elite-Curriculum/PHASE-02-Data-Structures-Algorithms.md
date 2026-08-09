# Phase 2 — Data Structures, Algorithms & Computational Thinking

> **Mission line:** Learn DS&A by building the structures your product actually needs — and by understanding their cost — instead of grinding abstract puzzles.
> **Placement:** The "I choose structures by their cost, not their familiarity" spike. Where good decisions get their first measurable teeth.
> **Prerequisite:** Phase 1. You can write pure functions and reason about memory.

---

## 🧭 The mental-model shift
Juniors reach for the structure they know (arrays and objects, always). Seniors reach for the structure whose *cost profile* fits the access pattern — and can tell you the Big-O and what `n` is in production. Algorithmic choice is a decision with a measurable price tag; this phase is where "make good decisions" stops being a slogan and becomes something you can defend with numbers.

## 🎯 Mission
Implement the data structures PulseBoard genuinely needs — a ring buffer for recent history, a min-heap scheduler, a hash table (to understand what `Map` is) — and annotate your whole core with complexity. You'll feel the difference between choosing a structure and defaulting to one.

## 🧠 What you must command (senior tier)
- **Big-O in your bones:** O(1), O(log n), O(n), O(n log n), O(n²) — and, crucially, *what `n` is in your system* (monitors? checks per monitor? total events?). Time vs space tradeoffs. Amortized analysis (why dynamic-array append is amortized O(1)).
- **How computers store data:** contiguous memory (arrays) vs pointer-linked (lists); why array *access* is O(1) but front insertion is O(n); how contiguity maps to CPU cache behavior (locality is why arrays often beat "theoretically equal" structures).
- **Arrays deeply:** static vs dynamic, the doubling growth strategy, why `unshift`/`splice` at the front is O(n).
- **Hash tables:** the hash function, buckets, **collisions** (chaining vs open addressing), load factor and resizing, why average lookup is O(1) but worst case is O(n). This is what JS objects and `Map`/`Set` are underneath.
- **The structures a monitoring system needs:**
  - **Ring buffer (circular buffer):** fixed-size recent-history window without unbounded growth — the *correct* fix for the leak you hand-patched in Phase 1.
  - **Priority queue / min-heap:** "which monitor is due next?" A heap keyed by next-check-time gives O(log n) insert/extract and scales past one-timer-per-monitor.
  - **Queue (FIFO):** the alert/notification backlog.
  - **Time-bucketed aggregation:** grouping checks into minute/hour buckets — a hash map keyed by time bucket, powering your charts.
- **When *not* to hand-roll:** in production you use battle-tested `Map`/`Set`/libraries — but you understand the internals so you know *why* and *when* their guarantees hold.

## 🏔️ Staff/elite extras
- **Choosing by access pattern, not by structure:** write-heavy vs read-heavy, point lookups vs range scans, ordered vs unordered — the same data wants different structures depending on how it's touched. This reasoning transfers directly to database indexing (Phase 6).
- **The cost of the wrong `n`:** an O(n²) that's fine at n=100 is an outage at n=100,000. Seniors reason about the growth curve *and* the realistic ceiling. Sometimes the "worse" algorithm is correct because n is tiny forever.
- **Cache-awareness:** why an array of structs can crush a linked list of the same data in practice despite equal Big-O — memory locality and cache lines. Rare knowledge; marks you as someone who understands the machine.
- **Premature-optimization discipline:** measure first (you have benchmarks from Phase 1), optimize the *proven* hot path only. Choosing the right structure usually beats micro-optimizing the wrong one.

## 📚 Resources
- **Your cheatsheet's** Data Structures section (arrays, hash tables, collisions) — implement each as you read.
- **Course:** [freeCodeCamp — JavaScript Algorithms & Data Structures](https://www.freecodecamp.org/learn/javascript-algorithms-and-data-structures/) — the DS portions.
- **Read:** [MDN — Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) and [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set), focusing on *when* to use them over objects/arrays.
- **Visual:** [VisuAlgo](https://visualgo.net/) — watch heaps and hash tables animate.
- **Watch:** search "Big O notation Gayle Laakmann McDowell HackerRank" (~10 min, by the author of *Cracking the Coding Interview*).
- **Book:** *Grokking Algorithms* — Bhargava. The friendliest DS&A book ever written; read the heap and hash chapters.
- **Meta:** [teachyourselfcs.com](https://teachyourselfcs.com/) — for filling CS-foundation gaps over the long term (algorithms, plus the durable CS you'll want eventually).

## 🏗️ Tickets
- [ ] **PB-201 — Ring buffer.** `RingBuffer(capacity)` with `push`, `toArray`, `size`. Replace each monitor's unbounded history. **Acceptance:** memory stays flat over a 1-hour run (verify via heap snapshot — callback to PB-104).
- [ ] **PB-202 — Min-heap scheduler.** Binary min-heap (`insert`, `extractMin`, `peek`) keyed by `nextCheckAt`. `HeapScheduler` uses a single timer that sleeps until the next-due monitor. **Acceptance:** 1,000 monitors use one active timer, not 1,000; you can explain why it scales.
- [ ] **PB-203 — Hash table from scratch.** `HashTable` with a hash function, bucket array, and **chaining**. Test that forces collisions and proves lookups still work. Benchmark vs native `Map`; write the gap up honestly (native wins — know why). **Acceptance:** collision test passes; benchmark documented.
- [ ] **PB-204 — Complexity annotations.** JSDoc `@complexity O(...)` on every non-trivial core function. Anything worse than O(n log n) gets justified or fixed. **Acceptance:** every core function annotated and defensible.
- [ ] **PB-205 — Time-bucket aggregation.** `bucketByHour(checks)` → uptime% and avg latency per hour via a `Map` keyed by hour-timestamp. Powers the 24-hour chart. **Acceptance:** correct buckets across day boundaries.
- [ ] **PB-206 — ADR-003.** "Heap scheduler vs timer-per-monitor," including the honest note that *below ~100 monitors, timer-per-monitor is completely fine.* Knowing that threshold is the senior move. **Acceptance:** ADR written.

## ⚙️ Best practices & professional habits
- Always name your `n` and its realistic ceiling. "This is O(n)" is meaningless until you say what `n` is and how big it gets.
- Prefer built-in `Map`/`Set` in real code; hand-roll only to learn. Know the internals *and* know not to reinvent them.
- Measure before optimizing — optimize only the proven hot path.
- Choose the structure for the dominant access pattern.

## ⚠️ Anti-patterns to kill
- Reaching for arrays/objects for everything regardless of access pattern.
- Front-inserting into big arrays in a loop (silent O(n²)).
- Optimizing code you never measured.
- Reinventing `Map` in production because you can.

## 🗣️ How a senior talks about this
"That's O(n log n) where n is checks-per-monitor, which caps around 10k, so it's fine — the real cost is the per-monitor loop outside it." · "A ring buffer bounds memory at the price of losing old history, which is exactly the tradeoff we want here." · "One heap-scheduled timer instead of a thousand intervals — same behavior, far less scheduler churn." · "Native `Map` beats my hash table because of engine-level optimization and better collision handling; I built mine to understand the guarantees, not to ship it."

## ✅ Checkpoint (out loud, cold)
1. Why is a ring buffer the right fix for unbounded history, and what do you *lose*?
2. Walk through a hash-table lookup including a collision. Worst-case complexity and when it happens?
3. Why does a min-heap scheduler scale better than one timer per monitor — and at what scale does it stop mattering?
4. Big-O of your core stats functions; which is most expensive and why?
5. Two structures with the same Big-O — why might one crush the other in practice?

## 🔗 Connects to
The ring buffer completes the memory story from Phase 1. The access-pattern reasoning is *exactly* how you'll think about database indexes in Phase 6. The heap scheduler is your first real scalability decision, revisited under load in Phase 9.
