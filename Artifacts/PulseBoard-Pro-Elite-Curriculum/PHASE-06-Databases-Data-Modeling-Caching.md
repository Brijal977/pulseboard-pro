# Phase 6 — Databases, Data Modeling & Caching

> **Mission line:** Replace files with a real database and learn to model data, read query plans, and cache like an engineer who's been paged at 3am for a slow query.
> **Placement:** The data spike. Monitoring generates time-series data at volume — the perfect teacher for indexing, transactions, and performance.
> **Prerequisite:** Phases 0–5. You have a clean repository layer to plug a real DB into.

---

## 🧭 The mental-model shift
Juniors treat the database as a bucket you put things in and get things out of. Seniors know the database is usually where systems live or die under load — and that *how you model and index data* determines whether a query takes 2ms or 2 minutes at scale. They model around query patterns, read execution plans, and know exactly which invariants need a transaction. Getting data wrong is expensive to fix later because data outlives code.

## 🎯 Mission
Move PulseBoard from JSON files to a real relational database with a designed schema, migrations, parameterized queries, proper indexing under realistic volume, transactions for multi-row invariants, and a caching layer — then know exactly when you'd outgrow it.

## 🧠 What you must command (senior tier)
- **Relational fundamentals:** tables, primary/foreign keys, normalization (1NF/2NF/3NF) and *deliberate* denormalization, one-to-many and many-to-many (join tables).
- **SQL fluency:** JOINs (inner/left/right), `GROUP BY` + aggregates, subqueries, `HAVING`, window functions (rolling uptime), and `EXPLAIN`/`EXPLAIN ANALYZE` to read a query plan.
- **Indexes — the senior topic:** B-tree indexes and what they cost (slower writes, storage), composite indexes and *column order*, covering indexes, when an index is *not* used, and the **N+1 query problem** (how to spot and kill it).
- **Transactions & ACID:** atomicity, consistency, isolation, durability; isolation levels (read committed → repeatable read → serializable) and the anomalies each prevents (dirty / non-repeatable / phantom reads); when you actually need a transaction.
- **Concurrency control:** optimistic (version column) vs pessimistic (row locks); deadlocks and how they arise.
- **Parameterized queries & SQL injection:** why string-concatenated SQL is the classic #1 vulnerability and why parameterization fixes it *structurally* (data can never become code).
- **Caching:** cache strategies (cache-aside, read-through, write-through/behind), TTLs, and the two hard problems — **invalidation** and naming. Redis as the workhorse. Cache stampede/thundering herd and how to prevent it.
- **SQL vs NoSQL:** when document, key-value, or time-series stores fit; why *you* use SQLite/Postgres and what would push you off it (a system-design conversation).
- **Migrations:** schema changes as versioned, forward-only, reviewable code — never hand-edited in prod.
- **Connection pooling:** why, and what happens without it under load.

## 🏔️ Staff/elite extras
- **Model for the read path:** you optimize for how data is *queried*, not just how it "naturally" decomposes — sometimes denormalizing or precomputing is correct. This is the same access-pattern reasoning as Phase 2's data structures, now at the storage layer.
- **Time-series realities:** monitoring data grows forever; retention policies, batched pruning, downsampling/rollups (raw → hourly → daily), and why a dedicated time-series store (or partitioning) eventually beats a generic table. Directly relevant to PulseBoard's core.
- **The migration threshold thinking:** naming the *specific* signals that would move you from SQLite → Postgres → sharding/replication (write concurrency, dataset size, availability needs). Seniority is knowing the threshold, not just the tools.
- **Reading a query plan like a story:** seq scan vs index scan vs index-only scan, join strategies (nested loop / hash / merge), and row-estimate vs actual — the skill that turns "the app is slow" into "this query needs a composite index on (monitor_id, checked_at)."
- **Consistency and durability tradeoffs** (fsync, write-ahead logging, replication lag) — a bridge into the distributed-systems reasoning of Phase 9.

## 📚 Resources
- **Interactive (best free SQL):** [SQLBolt](https://sqlbolt.com/) (all lessons) · [PostgreSQL Exercises](https://pgexercises.com/) (through window functions).
- **Read (the classic that makes you dangerous):** [Use The Index, Luke!](https://use-the-index-luke.com/) — how indexes actually work. Read the early chapters carefully.
- **Docs:** [better-sqlite3](https://github.com/WiseLibs/better-sqlite3/blob/master/docs/api.md) (your DB now) · [node-postgres (pg)](https://node-postgres.com/) · [Postgres EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html) · [Redis docs](https://redis.io/docs/latest/).
- **Watch:** Hussein Nasser's database engineering playlist (search "Hussein Nasser database engineering") — deep, practical on indexes, isolation, pooling.
- **Book (the most important backend book, career-long):** *Designing Data-Intensive Applications* — Kleppmann. Start Chapter 3 (storage & indexes). You'll return to it for years.

## 🏗️ Tickets
- [ ] **PB-601 — Schema design.** Design `users`, `monitors`, `checks`, `incidents`, `api_keys`. ER diagram in `/docs/schema.md`. Decide keys and *which columns are indexed and why*. **Acceptance:** ER diagram + a written justification for every index.
- [ ] **PB-602 — Migrations.** A forward-only migration runner (or a light lib); each change is a numbered file. **Acceptance:** dropping and rebuilding from migrations reproduces the schema exactly.
- [ ] **PB-603 — Repositories, parameterized only.** `monitorRepo`, `checkRepo`, `incidentRepo` using *only* parameterized queries. **Acceptance:** a test proves `'; DROP TABLE monitors;--` as a name is stored as literal data, not executed.
- [ ] **PB-604 — Kill an N+1.** Deliberately write the dashboard query as N+1 (one query per monitor for its latest check), measure it, then fix with a JOIN or window function. Capture both `EXPLAIN` plans. **Acceptance:** measurable speedup; you can read both plans.
- [ ] **PB-605 — Indexing under load.** Seed 1,000,000 fake checks. Run "checks in last 24h for monitor X" without an index (`EXPLAIN ANALYZE`), add the right composite index, re-run. **Acceptance:** plan changes from scan to index lookup; timing drops.
- [ ] **PB-606 — Transactions.** Wrap "open incident + update monitor status + record check" in a transaction. **Acceptance:** a simulated mid-transaction failure rolls back cleanly; you can name the isolation level chosen and the anomaly it prevents.
- [ ] **PB-607 — Caching layer.** Add Redis (or an in-process cache with the same interface) for the dashboard summary; cache-aside with TTL and explicit invalidation on writes. **Acceptance:** repeated dashboard loads hit the cache; a write invalidates correctly; you can explain the stampede risk and your mitigation.
- [ ] **PB-608 — Retention & rollups.** A scheduled job prunes raw checks older than N days and rolls them into hourly aggregates. **Acceptance:** old rows removed in batches without long table locks; charts still work from rollups.
- [ ] **PB-609 — ADR-005.** "SQLite now, Postgres later — the migration triggers." **Acceptance:** names the specific thresholds (concurrency, size, availability).

## ⚙️ Best practices & professional habits
- Model around query patterns, not just the real world; optimize for the read path.
- Every query hitting a growing table uses an index or is explicitly justified.
- Never build SQL by string concatenation. Ever. Parameterize.
- Wrap multi-statement invariants in transactions; reason about what a crash between statements leaves behind.
- Migrations are forward-only and reviewed like code.
- Time-series data needs a retention policy from day one.
- Cache invalidation is a design decision, not an afterthought.

## ⚠️ Anti-patterns to kill
- String-concatenated SQL (injection).
- Missing indexes on filtered/joined columns of big tables.
- N+1 queries (a query in a loop).
- Hand-editing production schema.
- Unbounded time-series growth with no retention.
- Caching with no invalidation strategy (stale data forever) or no TTL (memory leak).

## 🗣️ How a senior talks about this
"That's an N+1 — one query per monitor in a loop; a single JOIN collapses it." · "The composite index needs monitor_id first, then checked_at, because that matches the filter-then-range access pattern." · "This needs a transaction at read-committed; the anomaly I'm preventing is a lost update on the status." · "We cache the summary aside with a 30s TTL and invalidate on write; the stampede risk is handled with a short lock." · "We'd move to Postgres when write concurrency exceeds what SQLite's single-writer model handles."

## ✅ Checkpoint (out loud, cold)
1. What does an index cost, and why isn't "index everything" the answer?
2. Explain the N+1 problem with a PulseBoard example and two fixes.
3. Walk through ACID with a concrete PulseBoard transaction — which isolation anomaly are you preventing?
4. How does parameterization prevent SQL injection *structurally*?
5. Cache-aside: describe the read path, the write path, and the stampede risk.
6. What would push PulseBoard from SQLite to Postgres?

## 🔗 Connects to
Access-pattern reasoning comes straight from Phase 2. Parameterized queries are a Phase 7 security control. Caching, retention, and consistency tradeoffs open directly into distributed systems (Phase 9). Query performance is something you'll observe in production (Phase 10).
