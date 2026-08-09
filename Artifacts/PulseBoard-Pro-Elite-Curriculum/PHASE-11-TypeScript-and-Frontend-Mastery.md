# Phase 11 — TypeScript & Frontend Mastery

> **Mission line:** Now that you understand what they abstract, *earn* the modern tools: TypeScript as a design instrument, and React with real understanding — plus the frontend fundamentals most "React developers" skip.
> **Placement:** Range. You've been deep on the backend; this makes you genuinely full-stack and lets you pick up any framework in a weekend because you know what they're *for*.
> **Prerequisite:** Phases 0–10. Critically, you've hand-built the primitives frameworks hide.

---

## 🧭 The mental-model shift
This is the payoff of the whole "delay frameworks" philosophy. Because you built a raw HTTP server, hand-rolled DOM rendering (your 30-day plan), managed state manually, and wrote your own async utilities, you now see React and TypeScript for what they are — *solutions to specific problems you have personally felt*. That's the difference between a developer who can only operate inside a framework and an engineer who *chose* it and could rebuild its core ideas. TypeScript isn't "JavaScript with annotations"; it's a design tool that moves whole classes of bugs from runtime to compile time. React isn't magic; it's "UI as a function of state" plus a reconciler.

## 🎯 Mission
Migrate the codebase to TypeScript (types shared between server and client so the API contract is compile-time enforced), then rebuild the PulseBoard dashboard in React with genuine understanding of *why* each piece exists — and make it fast, accessible, and correct.

## 🧠 What you must command (senior tier)

**TypeScript as a design tool**
- **The point of types:** move errors from runtime to compile time; types as machine-checked documentation and as design (make illegal states unrepresentable).
- **The type system:** primitives, unions & intersections, literal types, **discriminated unions** (perfect for `MonitorStatus`), `interface` vs `type`, generics (and generic constraints), `unknown` vs `any` (and why `any` is a hole in the type system while `unknown` is honest), narrowing & type guards, `readonly`.
- **Utility types:** `Partial`, `Pick`, `Omit`, `Record`, `Returntype`, and *why* they reduce duplication.
- **`strict` mode from day one** (`strictNullChecks` especially — it eliminates a huge bug class).
- **Typing the boundaries:** validate external data at the edge (Zod → inferred types) so that *inside* the app the compiler guarantees shape. Types are not a substitute for runtime validation of untrusted input.
- **Shared types:** one source of truth for API request/response types, imported by both server and client — so a breaking API change is a *compile error*, not a production incident.

**React with understanding**
- **The core idea:** declarative UI as a pure function of state (`UI = f(state)`) — you built the imperative version by hand, so you feel the win. The virtual DOM and reconciliation as an optimization over your manual re-renders.
- **Components, props, composition** (the same composition-over-inheritance from Phase 1, now in UI).
- **Hooks, correctly:** `useState`, `useEffect` (and its genuine purpose + its overuse), the dependency array and why it exists, `useRef`, `useMemo`/`useCallback` (when they help and when they're noise), custom hooks for reuse.
- **State, the senior view:** local vs lifted vs server state; **server state belongs in a data-fetching library** (TanStack Query), not hand-rolled `useEffect` fetch soup — know why.
- **Data fetching & async UI:** loading/error/empty/success states as first-class, not afterthoughts.
- **Real-time:** Server-Sent Events (or WebSockets) to push live status to the dashboard — connecting your Phase-4 event engine to the browser.

**Frontend fundamentals most React devs skip**
- **The browser rendering pipeline:** DOM → CSSOM → render tree → layout → paint → composite; what triggers reflow/repaint; why layout thrash is slow.
- **Core Web Vitals & performance:** LCP, CLS, INP; bundle size, code splitting, lazy loading.
- **Accessibility (a11y) — non-negotiable for elite frontend:** semantic HTML first, ARIA only when needed, keyboard navigation, focus management, contrast, screen-reader basics. Most developers are weak here; competence is differentiation and it's the right thing to do.
- **Client-side security recap:** XSS via `dangerouslySetInnerHTML`, why tokens in `localStorage` are XSS-exposed (ties back to Phase 7's `HttpOnly` cookies).

## 🏔️ Staff/elite extras
- **Types as architecture:** discriminated unions and exhaustiveness checks that make whole categories of bug *impossible to write*; designing types so the compiler enforces your invariants. "Make illegal states unrepresentable" is a mindset, not a trick.
- **Framework-agnostic judgment:** because you understand the primitives, you can evaluate React vs Vue vs Svelte vs Solid on their *actual tradeoffs* (reconciliation vs fine-grained reactivity vs compilation) rather than hype — and learn the next one fast.
- **Knowing when NOT to reach for React:** a static page doesn't need a SPA; sprinkles of vanilla JS or a lighter tool are sometimes correct. Senior frontend judgment includes restraint.
- **Performance as measurement, not vibes:** profiling with the React DevTools Profiler and browser Performance panel, fixing the *measured* bottleneck (re-renders, bundle, layout) — the same "measure first" discipline as the backend.
- **The a11y-and-semantics-first instinct** that separates engineers who build for *all* users from those who ship a div soup that only works with a mouse.

## 📚 Resources
- **TypeScript (the best, free):** [Total TypeScript — Beginner's Tutorial](https://www.totaltypescript.com/tutorials) (Matt Pocock) · [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) · [type-challenges](https://github.com/type-challenges/type-challenges) for stretch.
- **React (the new official docs are excellent):** [react.dev — Learn](https://react.dev/learn), especially "Thinking in React," "You Might Not Need an Effect," and the Escape Hatches section.
- **Data/state:** [TanStack Query docs](https://tanstack.com/query/latest) — and read *why* it exists over `useEffect` fetching.
- **Build tooling:** [Vite](https://vitejs.dev/guide/) (know what esbuild/Rollup do under it — you earned this understanding).
- **Fundamentals:** [web.dev — Learn Accessibility](https://web.dev/learn/accessibility/) and [Core Web Vitals](https://web.dev/articles/vitals) · [MDN — Critical rendering path](https://developer.mozilla.org/en-US/docs/Web/Performance/Critical_rendering_path).

## 🏗️ Tickets
- [ ] **PB-1101 — Migrate to TypeScript (strict).** Convert the backend to TS with `strict: true`. Model `MonitorStatus` as a discriminated union; add an exhaustiveness check. **Acceptance:** no `any` except explicitly-justified, commented boundaries; the build is clean under strict.
- [ ] **PB-1102 — Shared types package.** Extract API request/response types into a shared module imported by server and client. **Acceptance:** changing a response type breaks the client *at compile time*.
- [ ] **PB-1103 — React dashboard.** Vite + React + TS. List monitors, their status, and a 24h uptime chart. Handle loading/error/empty/success explicitly. **Acceptance:** all four UI states are visibly handled, not just the happy path.
- [ ] **PB-1104 — Server state done right.** Data fetching via TanStack Query (caching, refetch, states) instead of `useEffect` fetch soup. Write `LEARNING.md` on the difference. **Acceptance:** you can explain why server state ≠ UI state and what Query gives you.
- [ ] **PB-1105 — Live updates.** SSE (or WebSocket) from the Phase-4 event engine pushes status changes to the dashboard in real time. **Acceptance:** a status flip appears without a manual refresh.
- [ ] **PB-1106 — Accessibility & performance pass.** Semantic HTML, keyboard-navigable, sufficient contrast, screen-reader-sane; then measure Web Vitals and fix the worst offender (bundle split or a wasteful re-render caught in the Profiler). **Acceptance:** navigable by keyboard alone; one measured performance issue fixed with before/after numbers.

## ⚙️ Best practices & professional habits
- `strict` TypeScript from the start; `any` is a last resort you comment and justify. Prefer `unknown` at boundaries.
- Validate untrusted input at runtime (Zod) *and* type it — types don't check data you didn't validate.
- Share types across the stack so contract drift is a compile error.
- Model state so illegal states can't be represented (discriminated unions, exhaustiveness).
- Handle loading/error/empty explicitly — the happy path is the minority of real UX.
- Semantic HTML first; accessibility is a requirement, not a nice-to-have.
- Reach for `useMemo`/`useCallback` when you've *measured* a need, not reflexively.

## ⚠️ Anti-patterns to kill
- `any` everywhere (you've disabled the type system).
- Trusting types for data that crossed a network boundary without runtime validation.
- `useEffect` fetch soup for server state (races, no caching, manual everything).
- Only rendering the happy path; ignoring loading/error/empty.
- Div soup, mouse-only UIs, missing labels/focus.
- Storing auth tokens in `localStorage` (XSS-exposed — use `HttpOnly` cookies).
- Cargo-culting `useMemo`/`useCallback` without measuring.

## 🗣️ How a senior talks about this
"`MonitorStatus` is a discriminated union, so the compiler forces me to handle every case — illegal states won't compile." · "Server state lives in Query, not `useState` — it has caching, refetching, and dedup I'd otherwise hand-roll badly." · "Types are shared, so a breaking API change is a red build, not a 3am page." · "I picked React for the reconciliation model here, but a static marketing page wouldn't need a SPA at all." · "Semantic HTML first; ARIA only where the platform doesn't already give me the semantics."

## ✅ Checkpoint (out loud, cold)
1. `unknown` vs `any` — why does `any` defeat the type system and where is `unknown` correct?
2. What problem does the virtual DOM solve, given you hand-wrote DOM updates before?
3. When do `useMemo`/`useCallback` actually help, and when are they noise?
4. Why does server state belong in a data-fetching library rather than `useEffect`?
5. Why are types alone insufficient for data crossing the network boundary?
6. Name three accessibility practices you'd apply to the dashboard and why they matter.

## 🔗 Connects to
Types formalize the API contract from Phase 5. Live updates surface the event engine from Phase 4 in the browser. The client-side security recap closes the loop with Phase 7. Composition-over-inheritance from Phase 1 reappears as component composition. This phase makes you full-stack — and the capstone (Phase 12) is where you present the whole thing.
