# Phase 3 — How the Internet Works

> **Mission line:** Learn the layer *underneath* every web request — TCP/IP, DNS, TLS, HTTP/1.1→2→3 — so the network stops being magic and becomes something you can reason about and debug.
> **Placement:** The elite fundamental most developers never learn. This is where your SRE background gives you a running start and where you pull decisively ahead of framework-only engineers.
> **Prerequisite:** Phases 0–2. You can run Node and read a stack trace.

---

## 🧭 The mental-model shift
Most developers' understanding of the network stops at "I call `fetch` and JSON comes back." When it breaks — a hung request, a CORS error, a mysterious 502, a TLS failure — they're helpless, because the whole stack below `fetch` is a fog. Elite engineers can trace a request from keystroke to pixel and back, name every hop, and know where to look when it fails. This knowledge *never expires* — it's true across every language, framework, and decade. Your operator background means you've seen these layers from the outside; now you'll own them from the inside.

## 🎯 Mission
Make PulseBoard's ping engine *network-literate*. It already fetches URLs; now it will understand DNS resolution, connection reuse, TLS, timeouts at each layer, redirects, and the difference between "the host is down" and "the host is slow" and "DNS failed" — the exact distinctions a real monitoring tool must report.

## 🧠 What you must command (senior tier)
- **The journey of a URL (the canonical interview question):** URL parsing → DNS resolution (recursive/authoritative, records, TTL, caching) → TCP handshake (SYN/SYN-ACK/ACK) → TLS handshake → HTTP request → server processing → response → rendering. Be able to narrate every step.
- **TCP/IP essentials:** the layered model (link/internet/transport/application), IP addressing, ports, what TCP guarantees (ordered, reliable, connection-oriented) vs UDP (fast, connectionless, lossy) and when each is right. The three-way handshake and connection teardown.
- **DNS deeply:** A/AAAA/CNAME/MX/TXT records, the resolver hierarchy, TTL and caching (and how stale DNS causes real outages), why DNS is a common failure point.
- **TLS/HTTPS:** what the handshake does (authentication via certificates, key exchange, then symmetric encryption), certificate chains and CAs, what SNI is, why "signed ≠ encrypted" mattered in Phase 1's JWT discussion, common failures (expired cert, hostname mismatch, chain issues). You'll monitor cert expiry — a real feature.
- **HTTP across versions:** HTTP/1.1 (persistent connections, head-of-line blocking, why 6-connections-per-origin), HTTP/2 (multiplexing over one connection, header compression, server push's rise and fall), HTTP/3/QUIC (over UDP, why it exists — eliminating TCP head-of-line blocking). Know *why* each version appeared.
- **HTTP semantics:** methods and their guarantees (safe, idempotent), status codes that mean something, headers (Content-Type, caching, compression, CORS), request/response anatomy.
- **CORS — properly:** the same-origin policy, why it exists (protecting the user, not the server), preflight requests, what actually fixes a CORS error (server headers) vs what juniors try (disabling it client-side). This alone saves you days over a career.
- **Latency & reliability realities:** round trips are the enemy (speed of light is a hard limit); connection reuse and keep-alive; timeouts at DNS/connect/TLS/response layers; retries and their dangers.

## 🏔️ Staff/elite extras
- **Reasoning about latency budgets:** knowing that each round trip costs real milliseconds shapes architecture — why you batch, why you colocate, why a chatty API is slow regardless of server speed. Back-of-envelope network math is a system-design skill (Phase 9).
- **Reading packets and connections:** `dig`, `curl -v`, `openssl s_client`, `traceroute`, browser Network tab waterfall analysis, and (your home turf) `tcpdump`/Wireshark basics. Being able to *observe* the network, not just theorize about it.
- **Head-of-line blocking as a recurring concept** — it appears at TCP, HTTP/1.1, and HTTP/2 layers, each solved differently. Understanding it once explains three protocol generations.
- **The security surface of the network:** why SSRF (Phase 7) is a network problem, why internal service exposure is dangerous, what a reverse proxy actually terminates.

## 📚 Resources
- **Book (free, the definitive resource, career-long):** [High Performance Browser Networking — Ilya Grigorik (hpbn.co)](https://hpbn.co/) — read the TCP, TLS, HTTP/1.1, HTTP/2 chapters. This is *the* text.
- **Read/watch:** search "What happens when you type google.com and press enter" — read a couple of the well-known write-ups; then narrate it yourself.
- **Craft:** [Julia Evans (jvns.ca)](https://jvns.ca/) networking zines — "How DNS works," her TLS and networking posts. The most approachable networking explanations anywhere.
- **Read:** [MDN — HTTP overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview), [Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods), [Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status), [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS), [Caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching).
- **Read:** [Cloudflare Learning Center](https://www.cloudflare.com/learning/) — excellent short explainers on DNS, TLS, HTTP/2/3, QUIC.
- **Tools to actually run:** `dig`, `curl -v`, `openssl s_client -connect host:443`, the browser Network tab.

## 🏗️ Tickets
- [ ] **PB-301 — Narrate the request.** In `/docs/network.md`, write the full "keystroke to pixel" journey for one PulseBoard dashboard request, every hop named. **Acceptance:** you can recite it in 3 minutes without notes.
- [ ] **PB-302 — Observe the layers.** Use `dig` (DNS), `curl -v` (headers + connection), and `openssl s_client` (TLS + cert) against a real monitored URL. Paste annotated output into `/docs/network.md`. **Acceptance:** you can point to the DNS answer, the TLS cert expiry, and the HTTP status in your captures.
- [ ] **PB-303 — Layered timeouts in the engine.** Distinguish and report DNS-failure vs connect-timeout vs TLS-error vs response-timeout vs HTTP-error in `pingUrl`. A real monitor must tell users *how* something is broken. **Acceptance:** five distinct failure modes produce five distinct, correct statuses.
- [ ] **PB-304 — Certificate expiry monitoring.** Add a check that reports days-until-expiry for HTTPS monitors and flags soon-to-expire certs. **Acceptance:** pointing at a site returns a correct expiry date; a near-expiry cert raises a warning.
- [ ] **PB-305 — Connection reuse.** Configure keep-alive / connection pooling in the engine; measure the latency difference vs a fresh connection per check. **Acceptance:** you observe and can explain the round-trip savings.
- [ ] **PB-306 — Redirect handling.** Follow redirects safely, capping the chain, recording the final URL, and (foreshadowing Phase 7) re-validating each hop's target. **Acceptance:** a redirecting URL resolves correctly; an infinite-redirect loop is bounded.
- [ ] **PB-307 — CORS, understood by doing.** When the browser dashboard (Phase 5/11) calls the API, deliberately trigger a CORS error, then fix it the *right* way (server headers). Write up what preflight is and why the fix belongs server-side. **Acceptance:** you can explain to a rubber duck why disabling CORS in the browser is not a fix.

## ⚙️ Best practices & professional habits
- Set timeouts at every network layer; a request with no timeout is a resource leak waiting to hang your process.
- Reuse connections (keep-alive/pooling); connection setup dominates latency for small requests.
- Treat DNS as a real dependency that fails and caches — many "random" outages are DNS.
- Report failures specifically (DNS vs connect vs TLS vs HTTP); "it's down" is a junior-grade error message.
- Fix CORS on the server, understanding *why* the browser enforces it.

## ⚠️ Anti-patterns to kill
- No timeouts, or one timeout for everything (can't tell slow-DNS from slow-server).
- "Just disable CORS" / `mode: 'no-cors'` cargo-culting.
- Treating all failures as a single opaque "error."
- Assuming DNS is instant and never stale.
- Opening a fresh connection per request at scale.

## 🗣️ How a senior talks about this
"That 502 is the reverse proxy telling us the upstream died — different from a 504, which is the upstream timing out." · "The latency is round trips, not compute; we're paying for a chatty protocol, so let's batch." · "The CORS preflight is failing because we don't return the right `Access-Control-Allow-Headers` — it's a server config, not a client bug." · "Cert chain's broken — the intermediate isn't being served; the leaf is fine."

## ✅ Checkpoint (out loud, cold)
1. Narrate everything that happens from typing a URL to seeing the page, every layer.
2. What does the TLS handshake accomplish, and what's the difference between it authenticating vs encrypting?
3. Why does HTTP/2 multiplex, and what problem did it solve from HTTP/1.1? What does HTTP/3 fix on top?
4. A request hangs. Walk through diagnosing whether it's DNS, connect, TLS, or the server.
5. What actually causes a CORS error and where is it correctly fixed?

## 🔗 Connects to
This is the foundation for the API contract (Phase 5) and for SSRF defense (Phase 7 — an SSRF is fundamentally a network-trust problem). Latency reasoning feeds system design (Phase 9). Cert/connection observation feeds production monitoring (Phase 10). Your ability to debug the network is now a permanent, framework-independent asset.
