# Phase 7 — Security Engineering

> **Mission line:** Build real multi-user auth *and* learn the attacker's whole playbook — because getting security wrong is how companies end up in the news, and getting it right is a defining senior signal.
> **Placement:** The phase you specifically asked to go deep on. We go all the way: authentication, authorization, and thinking like an adversary.
> **Prerequisite:** Phases 0–6. You have an API, a database, and understand the network.

---

## 🧭 The mental-model shift
Juniors think about the happy path: the honest user doing the expected thing. Security is the discipline of thinking about the *dishonest* path — the user who changes an ID in the URL, injects SQL, forges a request, or points your server at itself. Elite engineers hold an adversary in their head at all times and treat every input as hostile until proven otherwise. This is also where many self-taught engineers have the shakiest foundations, which makes real security competence pure differentiation.

## 🎯 Mission
Give PulseBoard real multi-user authentication and airtight authorization, then harden it against the attacks a real internet-facing service faces — with a special focus on the SSRF risk unique to a tool that fetches arbitrary user-supplied URLs.

## 🧠 What you must command (senior tier)

**Authentication (who are you?)**
- **Password storage:** never plaintext, never a fast hash (MD5/SHA). Use a **slow, salted, adaptive** hash — argon2id (current best practice), bcrypt, or scrypt. Salt defeats rainbow tables; work factor defeats brute force as hardware improves.
- **Sessions vs tokens (JWT) — the core tradeoff:** sessions = server-side state, opaque cookie, easy revocation, needs a store; JWT = stateless, self-contained, scales horizontally, but *revocation is hard* and it's widely misused. "Just use JWT" is a classic junior mistake — know both cold.
- **JWT internals:** header.payload.signature; HS256 (symmetric) vs RS256 (asymmetric); what belongs in a token (claims, `exp`) and what never does (secrets — it's *signed, not encrypted*; anyone can read it). The `alg: none` attack. Short-lived access + refresh token pattern.
- **Cookies for auth:** `HttpOnly` (JS can't read it → XSS can't steal it), `Secure` (HTTPS only), `SameSite` (CSRF defense), scoping.
- **MFA/2FA:** TOTP (shared secret + time), why it matters.
- **OAuth 2.0 & OpenID Connect:** what "Sign in with Google" actually does; authorization-code flow with PKCE; OAuth (delegated authorization) vs OIDC (authentication). Understand the flow even without implementing a provider.

**Authorization (what may you do?)**
- **AuthN vs AuthZ** — never blur them.
- **Access-control models:** RBAC (roles), ABAC (attributes), and the principle of **least privilege**.
- **Multi-tenancy authorization — the most common real bug:** user A reading user B's data by changing an ID (**IDOR**, Insecure Direct Object Reference). Every query must be scoped to the authenticated user. **Broken access control is OWASP #1.**

**The attacker's playbook**
- **OWASP Top 10** — know each: broken access control, injection, XSS, CSRF, security misconfiguration, sensitive-data exposure, SSRF, etc.
- **Injection** (SQL and beyond) — parameterization (Phase 6) as the structural fix.
- **XSS** (stored/reflected/DOM) — output encoding, CSP, never `innerHTML` with user data.
- **CSRF** — what it is, why SameSite cookies + CSRF tokens defend it.
- **SSRF — especially yours:** your engine fetches arbitrary user URLs; a malicious user could target `http://169.254.169.254/` (cloud metadata) or internal services. You *must* defend this.
- **Timing attacks** — constant-time comparison for secrets/tokens.
- **Rate limiting & lockout** — defending login from brute force.
- **Secrets management** — env/secret-manager, never in code or JWTs; rotation.
- **Supply-chain security** — `npm audit`, lockfiles, the risk of transitive dependencies, typosquatting; that most of your attack surface is code you didn't write.

## 🏔️ Staff/elite extras
- **Threat modeling:** systematically asking "what can go wrong here?" (STRIDE as a lens: Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Elevation of privilege). Doing a lightweight threat model for PulseBoard is a genuinely senior/staff artifact.
- **Defense in depth:** no single control is trusted; validate at the boundary *and* scope at the query *and* least-privilege the DB user. Layers, so one failure isn't catastrophic.
- **Security as a property of the whole system,** not a feature bolted on — it lives in your data model, your API contract, your infra, and your dependencies simultaneously.
- **The economics of security:** you're raising the attacker's cost, not achieving perfection; where to invest (auth, access control, injection, SSRF for you) vs where the risk is lower.
- **Fail closed, not open:** when an auth/authz check errors, deny — never default to allow.

## 📚 Resources
- **Bookmark forever:** [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/) — especially [Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html), [Authentication](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html), [Session Management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html), [Authorization](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html), [CSRF](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html), [SSRF](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html).
- **Read:** [OWASP Top 10](https://owasp.org/www-project-top-ten/) (all ten) · [The Copenhagen Book](https://thecopenhagenbook.com/) — modern, practical, framework-agnostic auth implementation, free and excellent.
- **JWT/OAuth:** [jwt.io](https://jwt.io/) · [Auth0 JWT Handbook](https://auth0.com/resources/ebooks/jwt-handbook) · [OAuth 2.0 Simplified — Aaron Parecki](https://www.oauth.com/) (free book by the spec editor).
- **Hands-on (free, world-class):** [PortSwigger Web Security Academy](https://portswigger.net/web-security) — do the **Authentication**, **Access control**, and **SSRF** tracks. This alone puts you ahead of most engineers on security.
- **Docs:** [argon2](https://github.com/ranisalt/node-argon2) or [bcrypt](https://github.com/kelektiv/node.bcrypt.js).

## 🏗️ Tickets
- [ ] **PB-701 — Password auth done right.** Signup/login with argon2id, strength rules, and **generic** error messages (never reveal whether the email or the password was wrong — that's user enumeration). **Acceptance:** DB stores argon2id hashes; identical passwords produce different hashes (salt works).
- [ ] **PB-702 — Secure sessions.** Server-side sessions with a store, cookie flags `HttpOnly; Secure; SameSite=Lax`, expiry and rotation on login. **Acceptance:** the session cookie is not readable from `document.cookie`.
- [ ] **PB-703 — Auth middleware + guards.** `requireAuth`; protected routes reject unauthenticated requests with 401. **Acceptance:** hitting a protected route without a session returns 401, not a crash or loop.
- [ ] **PB-704 — The IDOR test (the most important security ticket here).** Every monitor/incident query scoped to `req.user.id`. Test: user B requests user A's monitor by ID → 404 (not 403 — don't confirm existence). **Acceptance:** cross-tenant access is impossible; the test proves it.
- [ ] **PB-705 — SSRF defense on the engine.** Before pinging a user URL: reject non-http(s) schemes; resolve the host and **reject private/loopback/link-local ranges** (127/8, 10/8, 172.16/12, 192.168/16, 169.254/16, ::1, …); re-check after redirects. **Acceptance:** monitors pointed at `http://169.254.169.254/` or `http://localhost/` are refused with a clear error.
- [ ] **PB-706 — Login rate limiting & lockout.** Throttle per IP *and* per account; backoff or temporary lockout. **Acceptance:** rapid failed logins get throttled; you can explain why both dimensions.
- [ ] **PB-707 — API keys for the public API.** Issue keys (hashed in DB, shown once); the browser uses sessions, the API uses keys — two auth modes chosen deliberately. **ADR-006** on session-vs-token per surface. **Acceptance:** API auth via `Authorization: Bearer <key>`; keys stored hashed.
- [ ] **PB-708 — Timing-safe comparisons.** Audit token/key/secret comparisons; use `crypto.timingSafeEqual`. **Acceptance:** no secret compared with `===`.
- [ ] **PB-709 — Threat model + dependency audit.** A one-page STRIDE threat model in `/docs/threat-model.md`; run `npm audit` and address findings. **Acceptance:** the threat model names your top risks and their controls; no known high-severity dependency vulns.
- [ ] **PB-710 — TOTP 2FA (stretch).** Optional TOTP enrollment (QR) + verification. **Acceptance:** a standard authenticator app produces codes your server accepts in-window.

## ⚙️ Best practices & professional habits
- Slow, salted, adaptive hashing only. Never reversible.
- **Authorization ≠ authentication.** Authenticating tells you who they are; you *still* verify they may touch *this specific resource* on every request.
- Scope every query to the authenticated user; never trust an ID from the URL.
- Generic auth errors; don't leak whether an account exists.
- Cookies for auth: `HttpOnly + Secure + SameSite` always.
- Treat every user-supplied URL, string, and ID as hostile (SSRF, injection, XSS).
- Secrets in the environment/secret-manager; a JWT is readable by anyone (signing ≠ encryption).
- Constant-time comparison for secrets. Fail closed.

## ⚠️ Anti-patterns to kill
- Fast hashes or (unthinkably) plaintext passwords.
- Trusting a URL/path ID without an ownership check (IDOR).
- Fetching arbitrary user URLs with no SSRF guard.
- Putting sensitive data in a JWT thinking it's hidden.
- Specific auth errors ("no such user" vs "wrong password").
- `===` on secrets; defaulting to allow on error.

## 🗣️ How a senior talks about this
"We return 404 not 403 on someone else's resource so we don't confirm it exists." · "Argon2id with a per-user salt; fast hashes are a non-starter for passwords." · "The engine fetches user URLs, so SSRF is our sharpest risk — we resolve and block private ranges and re-check on redirect." · "Sessions for the browser, hashed API keys for the API; different surfaces, different threat models." · "Most of our attack surface is transitive dependencies, so lockfiles and audits are part of the pipeline."

## ✅ Checkpoint (out loud, cold)
1. Why is a fast hash wrong for passwords? What do salt and work factor each defend against?
2. Sessions vs JWT: full tradeoff, and which for (a) a browser dashboard and (b) a public API, with reasons.
3. What is IDOR, why is broken access control OWASP #1, and how does PulseBoard defend it?
4. Your engine fetches arbitrary URLs — walk through the SSRF risk and your exact defense.
5. Explain the `alg: none` JWT attack and how proper verification prevents it.
6. AuthN vs AuthZ in one sentence each, with a PulseBoard example of each.

## 🔗 Connects to
Builds on parameterized queries (Phase 6), the network-trust model (Phase 3), and the API contract/validation (Phase 5). Every defense here gets an attack-attempting test in Phase 8. Secrets management becomes concrete infrastructure in Phase 10.
