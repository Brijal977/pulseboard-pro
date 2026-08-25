# Issue templates

Copy-paste bodies. Every one of them answers What / Why / How you'll know it's done — the sections just differ in what evidence that takes.

Delete any section you cannot fill honestly. An empty heading is worse than a missing one: it looks answered.

---

## Bug

Adapted from the GitHub community's [recommended bug report format](https://github.com/orgs/community/discussions/147722). The load-bearing part is *Steps to Reproduce* — a bug you cannot reproduce is a spike, not a bug.

```markdown
### Description
<One paragraph. What is broken, for whom, and how bad.>

### Steps to Reproduce
1.
2.
3.

### Expected Behavior
<What should have happened, and why you believe that.>

### Actual Behavior
<What happened instead. Paste the error verbatim — not a paraphrase.>

### Environment
- OS:
- Node / browser version:
- Commit or version:

### Additional Information
<Logs, screenshots, the first commit you know it worked on, anything you already ruled out.>
```

**Worked example**

```markdown
### Description
Submitting the login form with a valid email crashes the app on iOS Safari. Desktop is unaffected. Blocks all mobile users.

### Steps to Reproduce
1. Open the app in iOS Safari 17.4
2. Enter a valid email and password
3. Tap "Log in"

### Expected Behavior
Redirect to /dashboard, as on desktop.

### Actual Behavior
White screen. Console: `TypeError: undefined is not an object (evaluating 'r.at(-1)')`

### Environment
- OS: iOS 17.4
- Browser: Safari
- Commit: a3f21c9

### Additional Information
`Array.prototype.at` is the suspect. Worked on 1.2.0; the polyfill was dropped in 1.3.0.
```

Note what the example does: it names the blast radius ("blocks all mobile users"), pastes the error exactly, and records one ruled-out hypothesis. It does **not** propose a fix — a bug report describes the world, and pre-committing to a cause narrows whoever picks it up.

---

## Feature

```markdown
### Problem
<The user-facing problem. Written so it stands on its own — no solution in it.>

### Proposed Solution
<What you'd build. One paragraph, not a design doc.>

### Alternatives Considered
<At least one, with why not. If you cannot name one, you have not thought about it yet.>

### Acceptance
- [ ] <falsifiable check>
- [ ] <falsifiable check>

### Out of Scope
<What this deliberately does not cover.>
```

Write *Problem* before *Proposed Solution* and resist folding them together. "We need a caching layer" is a solution wearing a problem's clothes; "the dashboard takes 4s to load because we refetch on every keystroke" is a problem, and it admits at least three solutions.

---

## Task / Chore

The default for scaffolding, config, deps, and cleanup. Most backlog items are this.

```markdown
## Goal
<One sentence. The outcome.>

## Why
<What stays broken or slow if this never happens. Be concrete.>

## Tasks
- [ ]
- [ ]

## Acceptance
<Falsifiable. Usually a command someone can run.>

## Blocked by
<#N, or delete this section.>
```

**Worked example**

```markdown
## Goal
Linting, formatting, and an npm script surface that stays the same for the life of the repo.

## Why
The scripts are an interface. Once `npm run lint|format|test|dev` exist, nobody has to remember project-specific incantations — and neither will CI later.

## Tasks
- [ ] ESLint flat config (`eslint.config.js`)
- [ ] Prettier + `.editorconfig` + `.nvmrc`
- [ ] npm scripts: `lint`, `format`, `test` (placeholder, exits 0), `dev`
- [ ] Plant an unused variable, confirm lint fails, remove it

## Acceptance
`npm run lint` exits non-zero on a deliberately-planted unused var; Prettier reformats on save.

## Blocked by
#1
```

---

## Spike

Timeboxed research. The deliverable is a decision, never code.

```markdown
## Question
<The one question this answers. If there are two, file two spikes.>

## Timebox
<Hours or days. Non-negotiable — that is the entire point of a spike.>

## Why now
<What decision is blocked until this is answered.>

## Deliverable
<Where the answer lands: an ADR, a comment on #N, a paragraph in DECISIONS.md.>

## Acceptance
The question is answered in writing, with the reasoning, and <blocked issue> can be scoped.
```

A spike that ends "we should probably use X" has failed. It ends with a decision and the reasoning that produced it, so nobody re-litigates it in three months.

If the timebox expires without an answer, that *is* the result — record what you learned and what you would try next, then close it. Silently extending a spike is how a week disappears.

---

## Docs

```markdown
## Goal
<What a reader will be able to do after this exists.>

## Audience
<Who — future you, a new contributor, an API consumer. Changes the register completely.>

## Tasks
- [ ]

## Acceptance
<Something checkable: a link resolves, a section exists, a stranger can follow the quickstart cold.>
```

The strongest docs acceptance criterion is a task, not a word count: "someone who has never seen this repo can get it running from the README alone."

---

## Drill

For deliberate practice. What distinguishes it: the artifact is disposable, the capability is the deliverable. Acceptance starts with **"you can…"**, never "the file exists".

```markdown
## Goal
<The capability. Starts with a verb you will be able to perform.>

## Why
<What ceiling this removes. The honest version.>

## Reps
- [ ]
- [ ]

## Acceptance
You can <observable thing you could not do before>.

## Reflection
Log in LEARNING.md: Learned / Confused by / Question for tomorrow.
```

**Worked example**

```markdown
## Goal
Fix a planted bug using only a debugger. No `console.log`.

## Why
`console.log` scales to about three variables and then stops. Breakpoints cost one afternoon to learn and pay back for the rest of your career.

## Reps
- [ ] Write a small buggy script: one off-by-one, one wrong-variable
- [ ] Fix it with breakpoints + step-through in VS Code
- [ ] Repeat with `node --inspect` from the terminal
- [ ] Set a conditional breakpoint (`i === 7`) and inspect a variable mid-loop

## Acceptance
You can set a conditional breakpoint and inspect a variable mid-loop.

## Reflection
Log in LEARNING.md: Learned / Confused by / Question for tomorrow.
```

The Reflection section is what stops a drill from being busywork — the rep teaches, but only the write-up survives past the week.
