---
name: gh-issue
description: Write GitHub issues that are actually actionable — pick the right template (bug, feature, task, drill, spike, docs), write falsifiable acceptance criteria, label and size them, and file them to the repo and project board. Use whenever the user wants to open, draft, rewrite, split, or groom a GitHub issue or backlog item, or convert notes/a curriculum/a TODO list into issues.
---

# Writing GitHub issues

An issue is a **contract with your future self**: it must be readable cold, by someone who has forgotten everything, and it must be possible to prove it is done. Everything below serves those two properties.

## Step 1 — Is this even an issue?

Do **not** open one for:
- Something you will finish in the next ten minutes. Just do it.
- A vague unease ("improve performance"). Turn it into a spike, or drop it.
- Three separate outcomes bundled together. Split them — see *Splitting* below.
- A question you can answer by reading the code. Read the code.

## Step 2 — Pick the template

Route on **what "done" looks like**, not on what the work feels like:

| If done looks like… | Template | Tell |
|---|---|---|
| Behaviour that is currently wrong becomes right | **Bug** | You can state expected vs actual |
| Behaviour that does not exist starts existing | **Feature** | A user can do something new |
| A change nobody outside will notice | **Task / Chore** | Config, scaffolding, deps, cleanup, refactor |
| A decision or an answer, not a code change | **Spike** | You do not yet know what to build |
| Prose is correct or exists | **Docs** | Output is words, not behaviour |
| *You* can do something you could not before | **Drill** | Practice rep; the artifact is disposable, the skill is not |

Ambiguous cases, resolved:
- *"It works but it's slow"* → Bug if there is a stated target it misses; Feature if the target is new; Spike if you do not know why.
- *"Refactor X"* with a user-visible reason → Task. With no reason → not an issue yet; find the reason first.
- *"Add tests for X"* → Task. Unless it is a rep to learn testing → Drill.
- *"Set up ESLint"* → Task, not Feature. Nobody outside the repo can tell.
- A curriculum or learning ticket → Drill if the acceptance is "you can…"; Task if the acceptance is "the file exists".

Full copy-paste bodies with worked examples: `references/templates.md`.

## Step 3 — Meet the quality bar

**Title.** `<prefix or ID> — <outcome in plain words>`. Say the outcome, not the activity: "Login button crashes the app on iOS" beats "Fix login bug". Under ~70 chars so it survives the board column.

**Every issue answers three questions in this order:**
1. **What** — one sentence, the outcome.
2. **Why** — what breaks or stays broken if this is never done. This is the field people skip and the one that decides, six weeks later, whether the issue is still worth doing.
3. **How you will know it is done** — acceptance criteria.

**Acceptance criteria must be falsifiable.** Someone else must be able to run a check and get a yes/no. Rewrite until a stranger could verify it:

| Not acceptance criteria | Acceptance criteria |
|---|---|
| "Linting works" | "`npm run lint` exits non-zero on an unused variable" |
| "The API is fast" | "p95 on `GET /pulses` under 200ms with 10k rows seeded" |
| "Understand git bisect" | "Bisect finds the bad commit among 8 in ≤3 steps, and I can explain why it is log(n)" |
| "Docs updated" | "README links `/docs/DECISIONS.md` and the link resolves" |

If you cannot write a falsifiable criterion, you do not understand the task well enough to file it. That is a signal, not a blocker — file a Spike instead.

**Scope.** One issue, one outcome. A `## Tasks` checklist is fine — it is the recipe. Two things in `## Acceptance` is two issues.

**Out of scope.** Add it whenever the title could plausibly be read wider than you mean. One line prevents an argument later.

## Step 4 — Classify

- **Type** (exactly one): `type:bug` `type:feature` `type:task` `type:spike` `type:docs` `type:drill`
- **Size** (exactly one): `size:S` (<1h) `size:M` (1–3h) `size:L` (half-day+). Sizing is not estimating — it exists to catch the L that is secretly three S's.
- **State**: `ready` (unblocked and fully specified — safe to start cold) or `blocked`. Everything else is backlog by default.
- **Milestone**: the phase or release it belongs to.
- **Dependencies**: GitHub has no dependency field, so write `## Blocked by` in the body with the issue number. Keep it in the body, not a comment — comments scroll away.

## Step 5 — File it

```bash
gh issue create --repo OWNER/REPO \
  --title "PB-00X — Outcome" \
  --label "type:task,size:S,ready" \
  --milestone "Phase 0 — Foundations" \
  --body-file /path/to/body.md
```

Use `--body-file`, not `--body`, for anything with backticks or code fences — the shell will otherwise eat them. Write the body to a scratch file first.

Add to a project board (needs the `project` scope: `gh auth refresh -s project,read:project`):

```bash
gh project item-add <PROJECT_NUMBER> --owner OWNER --url <ISSUE_URL>
```

To set the board column, look up the field and option IDs first — `gh project field-list <N> --owner OWNER --format json` — then `gh project item-edit --id <ITEM> --project-id <PID> --field-id <FID> --single-select-option-id <OID>`.

If `gh` is missing or unauthenticated, do not silently skip: say so, and produce a runnable script instead so the user can fire it after `gh auth login`.

## Splitting

Split when any of these is true:
- The acceptance section has an "and" joining two unrelated checks.
- Half of it is `ready` and half is blocked — split so the ready half can start.
- It would exceed ~300 changed lines in one PR. Review quality collapses past that, so the issue that produced it was too big.

When you split, keep the parent as a tracking issue with a checklist of children, and put the real acceptance criteria on the children.

## Grooming an existing issue

Ask, in order: Is the title an outcome? Is the Why still true? Is the acceptance falsifiable? Is it still one thing? Is it still `ready`? Anything that fails, fix in place. An issue nobody has touched in three months and that fails the Why test should be closed, not carried — a stale backlog makes the whole board unreliable.

## Bulk conversion (notes, curriculum, TODO list → issues)

1. One issue per line item; keep the source's IDs as title prefixes so the two stay cross-referenceable.
2. Route each through Step 2 individually — a list is rarely all one type.
3. Preserve the source's acceptance criteria **verbatim**. It is the author's own definition of done and rewording it loses intent; expand around it instead.
4. Add the Why yourself if the source omitted it — this is the highest-value thing you contribute.
5. Mark dependency order, and label the entry points `ready`.
6. Emit one idempotent script that creates labels, milestone, and all issues, then adds them to the board — re-runnable without duplicates.
