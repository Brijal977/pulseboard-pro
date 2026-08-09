# Phase 0 — The Engineer's Operating System

> **Mission line:** Before a single feature, install the workflow, tools, and *thinking habits* of a professional — including the two most underrated senior skills, debugging and code-reading.
> **Placement:** The substrate. Everything runs on this.
> **Prerequisite:** A computer and the refusal to stay junior.

---

## 🧭 The mental-model shift
Amateurs think the work is writing code. Professionals know the work is *changing a system safely over time* — and most of that is everything around the code: version control, reproducibility, small reversible steps, and the ability to diagnose what went wrong. A senior is trusted partly because their **process** is trustworthy. This phase builds the process so that in every later phase your energy goes to the hard problem, not to fighting your own setup.

## 🎯 Mission
Stand up the `pulseboard-pro` repository as a professional team-of-one: reproducible environment, real Git discipline, automated formatting/linting, decision records, and a debugging setup you can reach for instantly. No product code yet — just the forge itself.

## 🧠 What you must command (senior tier)
- **Git beyond add/commit/push:** branching (trunk-based with short-lived branches — what most modern teams actually do, vs GitFlow), `rebase` vs `merge` and what each does to history, interactive rebase to clean up before a PR, `cherry-pick`, `bisect` (binary-search your history for the commit that broke something), and `reflog` (your undo-of-last-resort — nothing committed is ever truly lost).
- **Why small PRs win:** review quality collapses as diff size grows; small PRs review faster, revert cleaner, and read as clearer history. This is a real engineering result, not a style opinion.
- **Conventional Commits** (`feat:`, `fix:`, `chore:`…) and **Semantic Versioning** — machine-readable history enables automated changelogs and communicates change magnitude to consumers. Know what a "breaking change" actually promises.
- **The tooling division of labor:** formatter (Prettier) ends all style debate; linter (ESLint) catches bugs and enforces rules; EditorConfig handles cross-editor basics. Decide once, automate forever — seniors don't argue about semicolons.
- **`package.json` truly understood:** dependency ranges (`^` caret vs `~` tilde vs pinned), `dependencies` vs `devDependencies`, what `package-lock.json` guarantees (byte-identical reinstalls) and why you commit it, npm scripts as your project's command API.
- **Runtime pinning:** `.nvmrc`/nvm — teams pin the Node version; "works on my machine" is a junior smell rooted in unpinned environments.
- **The shell as a power tool:** `grep`/`ripgrep`, `find`, pipes, `xargs`, `jq`, exit codes, `&&`/`||`. You have a sysadmin edge here — sharpen it toward development.

## 🏔️ Staff/elite extras (the parts most curricula skip)
- **Debugging as a discipline (start it now, reinforce it every phase):** reproduce → hypothesize → design the cheapest experiment that would *disprove* the hypothesis → bisect the search space → verify the fix and the root cause (not just the symptom). Guessing is the junior tell; diagnosis is the senior one.
- **Real debuggers, not just prints.** Learn breakpoints, step-over/into/out, watch expressions, and conditional breakpoints in VS Code and `node --inspect`. `console.log` is fine; over-reliance on it is a ceiling.
- **Reading code fast:** entry point first, trace one flow end-to-end, read tests to learn intent, follow the data. You'll read far more code than you write — train it deliberately.
- **The engineer's writing habit:** ADRs (Architecture Decision Records) and PR descriptions that state *what / why / how to verify*. Writing clarifies thinking; unwritten decisions get re-litigated forever.
- **Reproducibility mindset:** if it only works because of undocumented state on your machine, it doesn't work. This is the seed of everything in Phase 10.

## 📚 Resources
- **Interactive:** [Learn Git Branching](https://learngitbranching.js.org/) — do the rebase and advanced levels until they're reflexive.
- **Read:** [How to Write a Git Commit Message — cbeams](https://cbea.ms/git-commit/) · [Conventional Commits](https://www.conventionalcommits.org/) · [SemVer](https://semver.org/) · [Trunk-Based Development](https://trunkbaseddevelopment.com/).
- **Read:** npm docs on [package.json](https://docs.npmjs.com/cli/v10/configuring-npm/package-json) and [package-lock.json](https://docs.npmjs.com/cli/v10/configuring-npm/package-lock-json).
- **Debugging craft:** [Julia Evans — debugging zines & blog (jvns.ca)](https://jvns.ca/) — the best writing on the *craft* of debugging anywhere. Read "How to be a wizard programmer" and her debugging posts.
- **Docs:** [Node.js debugging guide](https://nodejs.org/en/learn/getting-started/debugging) · [VS Code debugging](https://code.visualstudio.com/docs/editor/debugging).
- **Book (career-long craft):** *The Pragmatic Programmer* — start it now, read a chapter a week alongside everything. It is the closest thing to a mentor in book form.

## 🏗️ Tickets
- [ ] **PB-001 — Repo & structure.** Create `pulseboard-pro`. Layout: `/src` (with `/core`, `/engine`, `/api`, `/web` to come), `/tests`, `/docs`, `/scripts`. `.gitignore` written *first* (node_modules, env, build artifacts). **Acceptance:** `README.md` says what the project is, how to run it, and links `/docs/DECISIONS.md`.
- [ ] **PB-002 — Tooling baseline.** ESLint (flat config) + Prettier + `.editorconfig` + `.nvmrc`. npm scripts: `lint`, `format`, `test` (placeholder), `dev`. **Acceptance:** `npm run lint` catches a deliberately-planted bug (unused var); Prettier reformats on save.
- [ ] **PB-003 — Git discipline switched on.** Conventional Commits from now on. Protect `main` in GitHub settings (PRs only, no direct push). **Acceptance:** your first five PRs are each < 300 changed lines with a "What / Why / How to test" description.
- [ ] **PB-004 — ADR habit.** Create `/docs/DECISIONS.md`. Write **ADR-001: "Vanilla-first, frameworks earned later"** in your own words. From now, every significant choice gets a 5-line ADR. **Acceptance:** ADR-001 exists.
- [ ] **PB-005 — Debugger drill.** Write a tiny buggy script (off-by-one + a wrong variable). Fix it *using a breakpoint and step-through in VS Code and `node --inspect`* — no `console.log` allowed. **Acceptance:** you can set a conditional breakpoint and inspect a variable mid-loop.
- [ ] **PB-006 — Bisect drill.** Make 8 commits, one of which "breaks" a test; use `git bisect` to find it. **Acceptance:** bisect identifies the bad commit in ≤ 3 steps; you understand why it's log(n).
- [ ] **PB-007 — Code-reading rep.** Clone a small, well-written OSS repo (e.g., a popular tiny utility library). Trace one function from entry to return and write a 5-line summary of what it does and one thing you'd do differently. **Acceptance:** the summary exists in `LEARNING.md`.
- [ ] **PB-008 — LEARNING.md ritual.** Template: *Learned / Confused by / Question for tomorrow*. **Acceptance:** first entry written.

## ⚙️ Best practices & professional habits
- Commit messages explain **why**; the diff already shows what.
- One logical change per commit; one concern per PR.
- Never commit secrets, `node_modules`, or generated files — `.gitignore` before your first commit.
- Automate style so you never think about it again. Debating semicolons is time theft.
- Reach for the debugger before the fifth `console.log`.
- Write the decision down while it's fresh; future-you is a different, more forgetful person.

## ⚠️ Anti-patterns to kill now
- **Giant "WIP" commits** and force-pushing over shared history.
- **`console.log`-only debugging** as a permanent method.
- **Guess-and-check debugging** — changing things randomly hoping one sticks. Diagnose, don't flail.
- **Unpinned everything** — no lockfile, no `.nvmrc`, "just install latest." Reproducibility is not optional.
- **Undocumented decisions** — you will re-argue them with yourself in three months.

## 🗣️ How a senior talks about this
"I keep PRs small because review quality drops sharply with size and reverts stay clean." · "We're trunk-based with short-lived branches; rebase locally to keep history linear, merge via PR." · "The lockfile guarantees the same dependency tree on CI and prod, which kills a whole class of 'works on my machine' bugs." · "First step on any bug is a reliable repro — I don't trust a fix I can't demonstrate."

## ✅ Checkpoint (out loud, cold)
1. `rebase` vs `merge`: what does each do to history, and when would you *not* rebase?
2. What does `package-lock.json` guarantee that `package.json` alone can't?
3. Walk me through diagnosing a bug you can reproduce but don't understand — what are your first three moves?
4. Why do teams protect `main` even when everyone is trusted?
5. Caret `^` vs tilde `~` in a dependency version — what updates does each allow?

## 🔗 Connects to
Feeds **every** later phase (this is the substrate). The debugging and code-reading habits started here are reinforced in Phases 1, 4, and 9. The reproducibility mindset becomes concrete infrastructure in Phase 10.
