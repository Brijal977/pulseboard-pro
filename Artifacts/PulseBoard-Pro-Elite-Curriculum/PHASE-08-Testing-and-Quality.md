# Phase 8 — Testing & Quality Engineering

> **Mission line:** Learn to prove your code works and keep it working — the discipline that lets you change a system confidently instead of fearfully.
> **Placement:** The confidence engine. Your literal goal — confidence — comes from a test suite you trust.
> **Prerequisite:** Phases 0–7. You have a real system with logic worth protecting.

---

## 🧭 The mental-model shift
Juniors test manually — click around, see if it works, move on — and dread changing old code because they can't tell what they'll break. Seniors encode expectations as tests, so change becomes safe and refactoring becomes routine. Tests aren't about catching typos; they're about *freedom to change*. A trusted green suite is what turns a scary codebase into a malleable one — and that safety is where real confidence lives.

## 🎯 Mission
Build a test suite for PulseBoard you actually trust: fast unit tests for the pure core, integration tests for the API, tests that *attempt the attacks* from Phase 7 and prove they fail, and the ability to test genuinely hard things (schedulers, retries, time).

## 🧠 What you must command (senior tier)
- **The testing pyramid:** many fast unit tests, fewer integration tests, few slow E2E tests — and why inverting it (the "ice cream cone") is painful and flaky.
- **Unit vs integration vs E2E:** what each proves and costs, and what to test at each level.
- **Test doubles:** stubs, mocks, spies, fakes — the differences, when each is appropriate, and the trap of over-mocking (testing your mocks instead of your code).
- **What makes a good test:** deterministic, isolated, fast, one reason to fail, tests *behavior* not *implementation* (the FIRST properties). Behavior-testing survives refactors; implementation-testing shatters on them.
- **Coverage honestly:** a floor-finding tool (what's totally untested), not a goal. 100% coverage of trivial getters while the hard logic is untested is "coverage theater."
- **TDD:** red → green → refactor; when it shines (well-understood logic, bug fixes) and when it doesn't (exploratory spikes).
- **Testing async & time:** fake timers to test retries/timeouts/schedulers without waiting in real time; avoiding flaky tests.
- **Testing the hard stuff via dependency injection:** inject the clock, the network, the DB so units are isolatable — this is why you designed for injection earlier.

## 🏔️ Staff/elite extras
- **Property-based testing:** instead of hand-picking examples, generate hundreds of random inputs and assert *invariants* (e.g., "uptimePercent is always between 0 and 100," "a ring buffer never exceeds capacity"). Catches edge cases you'd never think to write. A genuinely advanced technique most engineers never touch.
- **Mutation testing (awareness):** deliberately mutate your code and check whether tests catch it — a test of your tests. Knowing it exists signals depth.
- **Test design as system design feedback:** hard-to-test code is usually badly-designed code. When a thing is painful to test, the test is telling you the design has too much coupling — listen to it. Tests are a design pressure, not just a safety net.
- **The regression discipline:** every bug gets a failing test that reproduces it *before* the fix — so it can never silently return. This is how mature codebases stop rotting.
- **Testing at the right seam:** knowing whether a behavior belongs in a fast unit test or an integration test is judgment; over-testing at the E2E level makes suites slow and flaky, under-testing integration misses the bugs that live *between* units.

## 📚 Resources
- **Read (the best testing philosophy, free):** [Kent C. Dodds — Write tests. Not too many. Mostly integration.](https://kentcdodds.com/blog/write-tests) and [The Testing Trophy](https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications).
- **Read:** [Martin Fowler — Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html) and [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html).
- **Docs:** [Node.js built-in test runner (`node:test`)](https://nodejs.org/api/test.html) or [Vitest](https://vitest.dev/) · [Supertest](https://github.com/ladjs/supertest) for HTTP integration · [fast-check](https://github.com/dubzzz/fast-check) for property-based testing · [Playwright](https://playwright.dev/) for E2E.
- **Read:** [goldbergyoni/javascript-testing-best-practices](https://github.com/goldbergyoni/javascript-testing-best-practices) — comprehensive and practical.

## 🏗️ Tickets
- [ ] **PB-801 — Unit-test the pure core.** Full coverage of `/src/core` (stats, ring buffer, heap, memoize, pipe/compose), including edge cases (empty history, single element, boundaries). **Acceptance:** every core function tested; edges covered.
- [ ] **PB-802 — Test the async utilities with fake timers.** `retry`, `pLimit`, `delay` — tests run in milliseconds, not real seconds. **Acceptance:** a retry test that would take 30s runs instantly.
- [ ] **PB-803 — Integration-test the API.** Supertest against Express with a test DB: full CRUD, auth flows, validation errors, pagination. **Acceptance:** signup → login → create → list → delete, all asserted end to end.
- [ ] **PB-804 — Test the security guards (attack tests).** Explicit tests that *attempt* IDOR (PB-704), SSRF (PB-705), rate-limit bypass (PB-706), and SQL injection (PB-603) and assert they fail. **Acceptance:** each defense has a test that tries the attack and proves it's blocked.
- [ ] **PB-805 — Test the scheduler with an injected clock.** Refactor the scheduler to accept a clock; advance time programmatically and assert checks fire on schedule. **Acceptance:** you control time in the test with no real waiting.
- [ ] **PB-806 — Property-based tests.** Use fast-check on `stats` invariants (uptime ∈ [0,100]) and the ring buffer (size ≤ capacity, always). **Acceptance:** generated inputs find (or confirm the absence of) an edge-case bug.
- [ ] **PB-807 — One meaningful E2E.** A single Playwright test: load dashboard → add a monitor → see it appear. Runs headless (wired into CI in Phase 10). **Acceptance:** passes headless.
- [ ] **PB-808 — Coverage floor + honesty note.** Add coverage reporting and a reasonable threshold; write a note identifying what's high-coverage-low-value and low-coverage-high-risk. **Acceptance:** you can articulate where the suite is strong and weak.

## ⚙️ Best practices & professional habits
- Test behavior and contracts, not implementation — so refactors don't break tests.
- Fast and deterministic above all; a flaky test is worse than none — it erodes trust in the whole suite.
- Inject dependencies (clock, network, DB) so units test in isolation without real time or network.
- Write a failing test that reproduces every bug *before* fixing it.
- Don't chase 100% coverage; cover the code whose failure would hurt.
- Let test pain inform design — hard-to-test usually means badly-coupled.

## ⚠️ Anti-patterns to kill
- Manual click-testing as the only safety net.
- Over-mocking until you're testing mocks, not code.
- Testing private implementation details (brittle).
- Flaky tests left to rot (they poison trust in every green run).
- Coverage theater — high numbers, untested hard logic.
- Fixing a bug without a regression test.

## 🗣️ How a senior talks about this
"I test behavior, not internals, so I can refactor freely." · "That test was flaky because it depended on real time; it uses a fake clock now." · "Every security control has a test that tries the attack and asserts it fails." · "This function's a pain to test, which means it's doing too much — I'll split it." · "Coverage is a floor-finder, not a goal; the number's fine but this hot path is what I actually care about."

## ✅ Checkpoint (out loud, cold)
1. The testing pyramid — what goes wrong when it's inverted?
2. Stub vs mock vs spy vs fake — define each with a PulseBoard example.
3. How do you test retry-with-backoff without waiting real seconds?
4. Why is behavior-testing better than implementation-testing? Give an example that would create a brittle test.
5. What's coverage theater and how do you avoid it?
6. What's a property-based test, and what invariant would you assert for `uptimePercent`?

## 🔗 Connects to
Tests the pure core from Phase 1, the utilities from Phase 4, the API from Phase 5, and the defenses from Phase 7. The suite gets wired into CI in Phase 10, becoming the gate that protects `main`. This is the safety net that makes the refactors in Phase 9 and the TypeScript migration in Phase 11 low-risk.
