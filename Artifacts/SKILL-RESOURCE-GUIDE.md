# The Skill Resource Guide — Learn Every Competency

A companion to **`THE-PATH-Engineer-Skill-Tree.md`**, organized the same way: resources mapped to each of the 24 skills, across every format, free and paid.

> **How this differs from `RESOURCE-LIBRARY.md`:** that library is organized by the **PulseBoard project phases** (what to read *while building*). This one is organized by **skill** (what to study to level up a *specific competency*). Same philosophy applies: it's a menu, not a checklist. Each skill's ⭐ pick is the one to reach for first. Free-first; buy the few paid items only when you'll use them.

**Legend:** 🆓 Free · 💵 Paid (one-time) · 🔁 Subscription · 📗 Book · 🎥 Video/course · 🖥️ Interactive · 📄 Docs/article · ⭐ Top pick for that skill

*Prices and availability change, and newer resources may exist beyond my knowledge — verify before buying. The canonical books and free sites are stable.*

---

## The five books that recur across many skills

Buy these over time and you've covered a huge fraction of the map:

1. ⭐ **A Philosophy of Software Design** — Ousterhout 💵 (design, clean code, complexity, judgment)
2. ⭐ **The Pragmatic Programmer** — Hunt & Thomas 💵 (craft, judgment, career-wide)
3. ⭐ **Designing Data-Intensive Applications** — Kleppmann 💵 (system design, data, reliability)
4. **The Staff Engineer's Path** — Reilly 💵 (communication, influence, ownership, mentoring)
5. **Refactoring** — Fowler 💵 (refactoring, clean code, design)

---

# PILLAR I — The Craft of Writing Code

### 1. Programming & language fundamentals
- ⭐ 🖥️ **javascript.info** 🆓 — the best structured deep-dive for JS.
- 📗 **Eloquent JavaScript** — Haverbeke 🆓 · **You Don't Know JS Yet** — Simpson 🆓 (GitHub).
- 🎥 **Frontend Masters — "JavaScript: The Hard Parts" / "Deep JS Foundations"** 🔁.
- 🎥 **Lydia Hallie — JavaScript Visualized** (YouTube) 🆓 · 📄 **MDN** 🆓.
- *For true fundamentals across languages:* 🖥️ **Nand2Tetris** 🆓, 🎥 **Harvard CS50** 🆓.

### 2. Data structures & algorithms (applied)
- ⭐ 📗 **Grokking Algorithms** — Bhargava 💵 — friendliest DS&A book.
- 🖥️/🎥 **NeetCode** (neetcode.io) 🆓 — roadmap + clear videos.
- 📗 **A Common-Sense Guide to DS&A** — Wengrow 💵 · **Cracking the Coding Interview** — McDowell 💵.
- 🖥️ **VisuAlgo** 🆓 (visualize) · **Exercism** 🆓 (mentored practice) · **LeetCode** 🆓/🔁.
- 🎥 **Abdul Bari — Algorithms** (YouTube) 🆓 · **Princeton Algorithms (Sedgewick)** — Coursera 🆓 (audit).

### 3. Clean, readable code
- ⭐ 📗 **A Philosophy of Software Design** — Ousterhout 💵 — the best modern book on writing simple, deep code.
- 📗 **Clean Code** — Martin 💵 (read *critically* — great mindset, some dated dogma).
- 📄 **Google Style Guides** (google.github.io/styleguide) 🆓 · **naming** — search "Naming things in code" talks.
- 📗 **The Art of Readable Code** — Boswell & Foucher 💵 — practical, underrated.

### 4. Refactoring & taming complexity
- ⭐ 📗 **Refactoring** — Fowler 💵 — the named smells and moves.
- 🖥️ **refactoring.guru** 🆓/💵 — code smells + refactorings, illustrated.
- 📗 **Working Effectively with Legacy Code** — Feathers 💵 — refactoring code that fights back.
- 📗 **A Philosophy of Software Design** (again) — complexity as the real enemy.

### 5. Debugging & problem-solving
- ⭐ 🖥️ **Julia Evans — Wizard Zines** (wizardzines.com) 🆓 blog / 💵 zines — the craft of debugging, best anywhere.
- 🖥️ **MIT Missing Semester** (missing.semester) 🆓 — debuggers, profilers, tooling.
- 📗 **Debugging** — David Agans 💵 — nine timeless rules.
- 📄 **Node.js debugging** + **VS Code debugging** docs 🆓 · 🎥 browser DevTools guides 🆓.

---

# PILLAR II — Building Correct, Reliable Software

### 6. Testing & quality engineering
- ⭐ 📄 **Kent C. Dodds — "Write tests. Not too many. Mostly integration." + Testing Trophy** 🆓.
- 📗 **Unit Testing Principles, Practices, and Patterns** — Khorikov 💵 — best book on *what makes a good test*.
- 📗 **Test-Driven Development by Example** — Kent Beck 💵.
- 📄 **goldbergyoni/javascript-testing-best-practices** 🆓 · **Vitest / fast-check / Playwright** docs 🆓.
- 🎥 **Testing JavaScript** — Kent C. Dodds 💵 (guided depth).

### 7. Error handling & defensive programming
- ⭐ 📗 **Release It!** — Michael Nygard 💵 — designing for failure, stability patterns (bulkhead, circuit breaker). A classic.
- 📄 **nodebestpractices — Error Handling** (GitHub) 🆓 · **Node.js Errors docs** 🆓.
- 📄 **Joyent — Error handling in Node.js** (operational vs programmer errors) 🆓.

### 8. Secure coding & the adversarial mindset
- ⭐ 🖥️ **PortSwigger Web Security Academy** 🆓 — hands-on labs, the best free security training.
- 📄 **OWASP Top 10 + Cheat Sheet Series** 🆓 — bookmark forever.
- 📄 **The Copenhagen Book** 🆓 — practical auth · 🖥️ **Hacksplaining** 🆓.
- 📗 **The Web Application Hacker's Handbook** 💵 · 🎥 **PwnFunction** (YouTube) 🆓.

### 9. Performance & optimization (measure-first)
- ⭐ 📗 **Systems Performance** — Brendan Gregg 💵 (and brendangregg.com 🆓) — the definitive performance text; the USE method.
- 📗 **High Performance Browser Networking** — Grigorik 🆓 (hpbn.co) — web/network performance.
- 📄 **web.dev — Learn Performance / Core Web Vitals** 🆓 (frontend) · Node.js + Chrome profiling docs 🆓.

---

# PILLAR III — Design & Architecture

### 10. Software design (SOLID, coupling, abstraction)
- ⭐ 📗 **A Philosophy of Software Design** — Ousterhout 💵 — deep modules, simple interfaces.
- 📄 **refactoring.guru — SOLID + design principles** 🆓.
- 📗 **Clean Architecture** — Martin 💵 (read critically) — boundaries and the dependency rule.
- 📄 **Martin Fowler — bliki** (martinfowler.com) 🆓 — coupling, cohesion, layering.

### 11. Design patterns (judicious)
- ⭐ 🖥️ **refactoring.guru — Design Patterns** 🆓/💵 — illustrated, with *when not to use* each.
- 📗 **Head First Design Patterns** 💵 — the approachable classic.
- 📗 **Game Programming Patterns** — Nystrom 🆓 (free online) — superbly written, patterns explained better than most.
- 📄 Enterprise patterns — **Fowler's PoEAA** (reference) 💵.

### 12. System design & distributed systems
- ⭐ 📄 **System Design Primer** (GitHub) 🆓 — the best free resource.
- 📗 **Designing Data-Intensive Applications** — Kleppmann 💵 · **System Design Interview Vol 1 & 2** — Xu 💵.
- 🎥 **Martin Kleppmann — Distributed Systems lectures** (YouTube) 🆓 · **MIT 6.824** 🆓.
- 🎥 **ByteByteGo / Gaurav Sen / Hussein Nasser** (YouTube) 🆓 · 📄 **AWS Builders' Library** 🆓.

### 13. Data modeling & databases
- ⭐ 📗/📄 **Use The Index, Luke!** (use-the-index-luke.com) 🆓 — how indexes really work.
- 🎥 **CMU Intro to Database Systems (Andy Pavlo)** (YouTube) 🆓 — a real university course.
- 🖥️ **SQLBolt / PGExercises / Mode SQL Tutorial** 🆓 · 📗 **Database Internals** — Petrov 💵.
- 🎥 **Hussein Nasser — Database Engineering** (YouTube) 🆓.

---

# PILLAR IV — Working in the Real World

### 14. Version control & engineering workflow
- ⭐ 🖥️ **Learn Git Branching** 🆓 — until rebase is reflexive.
- 📗 **Pro Git** (git-scm.com/book) 🆓 — the complete reference.
- 🖥️ **MIT Missing Semester — Git** 🆓 · 📄 **Conventional Commits / SemVer / Trunk-Based Development** 🆓.

### 15. Code review (giving & receiving)
- ⭐ 📄 **Google — Code Review Developer Guide** (google.github.io/eng-practices) 🆓 — the standard, for reviewer *and* author.
- 📄 **Michael Lynch — "How to Do Code Reviews Like a Human"** 🆓 — the tone and empathy of review.
- 📄 **conventionalcomments.org** 🆓 — labeling feedback clearly · **Google — style guides** for what to leave to the linter.

### 16. Reading code & navigating large codebases
- ⭐ 📗 **The Programmer's Brain** — Felienne Hermans 💵 — how to read code and build mental models (backed by cognitive science).
- 📄 **"How to read source code"** articles + do it: read a small OSS repo weekly, trace one flow.
- 🖥️ **MIT Missing Semester** 🆓 — tools for navigating code fast (grep, ctags, LSP).

### 17. Shipping & operating in production
- ⭐ 📗 **Google SRE Books — all three** (sre.google/books) 🆓 — the operations bible, free.
- 🎥 **TechWorld with Nana** (YouTube) 🆓 — the best free DevOps channel (Docker, K8s, CI/CD).
- 📗 **The DevOps Handbook** 💵 / **The Phoenix Project** 💵 · **Observability Engineering** — Majors et al. 💵.
- 📄 **The Twelve-Factor App** 🆓 · Docker / GitHub Actions docs 🆓.

---

# PILLAR V — The Human Skills That Gate Seniority

### 18. Communication & technical writing
- ⭐ 📄 **Google — Technical Writing Courses** (developers.google.com/tech-writing) 🆓 — free, excellent, start here.
- 📗 **The Pyramid Principle** — Barbara Minto 💵 — structuring arguments; how consultants/execs write.
- 📄 **Engineering design-doc templates** — search "Google design doc" / "Uber RFC" 🆓 · **Stripe/Increment writing culture** articles 🆓.
- 📄 **"Writing for Engineers"** (blog posts by Heinrich Hartmann and others) 🆓.

### 19. Engineering judgment & decision-making
- ⭐ 📗 **The Pragmatic Programmer** — Hunt & Thomas 💵 — the craft of good calls.
- 📄 **adr.github.io — Architecture Decision Records** 🆓 — the tool for making judgment visible.
- 📄 **The Pragmatic Engineer** — Orosz (newsletter) 🔁/🆓 · **lethain.com** — Will Larson 🆓.
- 📗 **A Philosophy of Software Design** — tradeoff thinking throughout.

### 20. Estimation, scoping & execution
- ⭐ 📄 **Shape Up** — Basecamp (basecamp.com/shapeup) 🆓 — a sane, modern take on scoping and betting.
- 📗 **Software Estimation: Demystifying the Black Art** — McConnell 💵.
- 📄 **"Story points / #NoEstimates" debates** 🆓 — read both sides; form a view.

### 21. Collaboration, disagreement & influence
- ⭐ 📗 **The Staff Engineer's Path** — Reilly 💵 — influence without authority, done right.
- 📗 **Crucial Conversations** 💵 — high-stakes disagreement · **Never Split the Difference** — Voss 💵 (negotiation/empathy).
- 📄 **"Disagree and commit"** (search Amazon leadership principle + essays) 🆓.

### 22. Ownership & being trusted
- ⭐ 📗 **Extreme Ownership** — Willink & Babin 💵 — the ownership mindset (military framing; principle transfers).
- 📗 **The Staff Engineer's Path** / **StaffEng.com** 🆓 — what ownership looks like at scale.
- 📄 **The Pragmatic Engineer / lethain.com** 🆓 — real accounts of trusted senior behavior.

### 23. Mentoring & multiplying others
- ⭐ 📗 **The Staff Engineer's Path** — Reilly 💵 · **StaffEng.com** (stories) 🆓 — impact through people.
- 📗 **An Elegant Puzzle** — Will Larson 💵 (org/leadership leverage) · **The Manager's Path** — Fournier 💵 (even if staying IC, the mentoring chapters).
- 📄 **Do it now:** teach publicly (blog, answer questions, review OSS PRs) — the practice *is* the resource.

### 24. Learning how to learn (and working with AI)
- ⭐ 🎥 **"Learning How to Learn"** — Oakley & Sejnowski (Coursera) 🆓 — the most popular course on the science of learning.
- 📗 **Ultralearning** — Scott Young 💵 · **Make It Stick** — Brown et al. 💵 (evidence-based learning).
- 📗 **Pragmatic Thinking & Learning** — Andy Hunt 💵 — for programmers specifically.
- ⭐ 📄 **teachyourselfcs.com** 🆓 — what to learn and the best resource for each CS subject.
- 📄 *Working with AI:* use it to explain/quiz/scaffold *after* you understand the fundamentals — read your tools' docs, and treat the durable skill as your judgment to evaluate its output.

---

## If you only buy five books (in order)
1. **A Philosophy of Software Design** — Ousterhout (design, clean code, complexity)
2. **The Pragmatic Programmer** — Hunt & Thomas (craft + judgment, career-wide)
3. **Designing Data-Intensive Applications** — Kleppmann (data + system design)
4. **The Staff Engineer's Path** — Reilly (the human/seniority skills)
5. **Refactoring** — Fowler (safe change + design taste)

## If you spend zero dollars
The free path is complete: **javascript.info, NeetCode, refactoring.guru, Julia Evans, MIT Missing Semester, PortSwigger, System Design Primer, Google SRE Books, Google Technical Writing, teachyourselfcs, CS50, Full Stack Open** — plus the free YouTube channels (Hussein Nasser, ByteByteGo, TechWorld with Nana, Lydia Hallie). You can become elite on free resources alone; the paid ones just save time.

---

### Mentor's reminder
Same warning as always: collecting resources *feels* like progress and isn't. For whichever skill you're leveling up right now, take its ⭐ pick, use it to go one rung deeper, then get back to building and deciding. The map (skill tree) tells you *what* to grow; this guide tells you *where* to learn it; your actual growth happens in the editor, on real code, making real calls.
