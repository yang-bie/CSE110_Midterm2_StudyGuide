# CSE 110 Midterm 2 — Test-Day Map

The full review roadmap. Read top-to-bottom once; skim the **bold bits** the night before.

**Contents**
1. How the exam works
2. Question formats + how to attack each
3. The trade-off mindset (the meta-skill)
4. The -ilities
5. Architecture concepts
6. The 14 code patterns
7. High-level architecture patterns
8. Code smells (HTML / CSS / JS)
9. Smell → pattern → -ility map
10. Agile ↔ tooling (CI/CD, PRs, pipeline, flags)
11. Testing (pyramid, TDD/BDD, security)
12. Operating & software lifecycle
13. Documentation (ADR, JSDoc)
14. GenAI reasoning
15. Your team / project + your pipeline
16. Priority + where to drill

---

## 1. How the exam works

1. **Apply + reason in a situation, predict outcomes** — not recall. (Continues Midterm 1.)
2. **Evaluate code** — review / refactor / comment / **pick between** snippets. You will NOT write from scratch; constructs are given.
3. **Scope:** lectures ~May 8–28 (pattern code, PDF captures, links). **Excludes** Helena's participation standings. Fowler article → only the *gist* (A/B testing, feature toggles).
4. **GenAI reasoning** questions are expected.
5. **Team/project** questions = easy points.

---

## 2. Question formats + how to attack each

| Format | How to answer |
|---|---|
| **Define X in your own words** | Be precise + terse. Hit the essential idea, not a textbook quote. |
| **"What was the point of that question?"** | Explain *why it matters* / what skill it tests. |
| **List at least N…** | Just list cleanly; don't over-explain. |
| **Trade-off discussion** | Name the **-ilities gained AND sacrificed**; "it depends"; give a better option. |
| **Pros/cons of a metric** | Multiple angles, including **cost** and what it hides. |
| **Fill-in-the-blank** | Usually a keyword (appropriate, DRY, orthogonality, composed…). |
| **Concept table** | Description **+ the value/reason** it provides. |
| **Diagram analysis** | What's good, the typical complaint, "is this right for *every* case?" → usually **No, it depends on risk**. |
| **Named scenario** | Apply concepts to the character's situation; be defensive/critical. |
| **Code review / refactor / pick-between** | (a) name the **smell**, (b) the **-ility** at risk, (c) the **pattern/fix**; for pick-between, justify with coupling/testability/etc. |

---

## 3. The trade-off mindset (threads through everything)

- **"Appropriate," not "correct."** **"It depends."** **"All models are wrong, some are useful."**
- **Fight solution-first thinking** — understand the problem before picking a tool.
- **Every choice trades -ilities.** You can't max everything. Gaining performance may cost dev time; DI gains testability but costs simplicity; shadow DOM gains encapsulation but blocks global styles.
- **Tech debt vs tech investment** — shortcuts borrow against the future; refactoring/docs pay off later.
- **Premature optimization / abstraction / automation** = solving problems you don't have yet (the "Lambo problem"). **YAGNI / KISS.**
- This is the **Iron-Triangle-style** reasoning the TA flagged: most "discuss X" questions = "which -ilities does X help vs hurt?"

---

## 4. The -ilities (the WHY behind every design choice)

**The 8 ISO 25010 categories** (know these cold):

| -ility | Means | Verify with |
|---|---|---|
| **Functional suitability** | does the right things, correctly/completely | requirements review, unit + E2E tests, TDD |
| **Performance efficiency** | good use of time/resources | perf tests, Lighthouse, load timing |
| **Compatibility** | coexists/interoperates (browsers, devices) | browser/device tests, integration tests |
| **Usability** | learnable, operable; accessibility | usability tests, analytics, a11y checks |
| **Reliability** | keeps working under expected conditions/failures | load/fault-injection tests, retries, uptime |
| **Security** | protects data/actions from misuse | scanning, input validation, auth, dep scan |
| **Maintainability** | understand/test/modify/extend | static analysis, modular design, code review |
| **Portability** | move across devices/platforms/environments | device tests, containers, abstraction layers |

Also referenced: **accessibility, testability, modularity, scalability, availability, reusability, flexibility**.

**The move on the exam:** for a code/design choice → name the -ility, state the risk/benefit, give a verification method, suggest an improvement.

---

## 5. Architecture concepts (one-liners)

- **Cohesion** — related things grouped together (want **high**).
- **Coupling** — how dependent parts are on each other (want **loose**).
- **SRP** — one responsibility per part.
- **Separation of Concerns** — split logic / data / presentation.
- **DRY** — one source of truth; no duplication.
- **Orthogonality** — parts don't interfere with each other (reduces *interdependence*; ≠ DRY).
- **Encapsulation / data hiding** — `#private` fields; hand out **copies** (`[...arr]`), not internals.
- **Abstraction** — hide details so code can change without breaking unrelated code. Don't over-generalize.
- **Dependency Injection / IoC** — receive dependencies; the framework calls *your* code. Risk: "loss of control" if you don't understand it.
- **Minimalism** — less code = fewer bugs; "more code = more bugs" (E=MC²).

---

## 6. The 14 code patterns (recognize the fingerprint)

| # | Pattern | Fingerprint (what you SEE) | Kills | Main -ility | ⚠️ Gotcha |
|---|---|---|---|---|---|
| 1 | **Pub/Sub** | `dispatchEvent(new CustomEvent)` here, `addEventListener` elsewhere | tight coupling | maintainability | `composed:true` to leave shadow DOM; remove listener in `disconnectedCallback` |
| 2 | **Event Delegation** | ONE listener on parent + `event.target.closest()` | listener-per-child | performance/maintainability | works for dynamically-added children |
| 3 | **Notifications Hub** *(1+2)* | children emit, one parent routes by `event.detail` | parent querying child internals | maintainability | reads `detail`, never child DOM |
| 4 | **Components/Decomposition** | imports `-template` / `-styles` / `-i18n` | one huge file | maintainability | over-decomposition = more files |
| 5 | **MVC** | Model `extends EventTarget`; `connect(model,view)`; view fires `intent` | UI+data tangled | maintainability/testability | Model never touches the DOM |
| 6 | **Todo-MVC** *(5+4+2)* | `connect(model, input, list)` | — | — | capstone |
| 7 | **Strategy** | object of same-signature functions, picked by key | giant if/else | maintainability | shared signature = swappable |
| 8 | **Dependency Injection** | component takes `storage`/`service` from outside | hardcoded backend | testability/portability | depend on interface, not `localStorage` |
| 9 | **Sortable/Storable** *(7+8)* | two independent attributes (sort + storage) | — | — | capstone, independent axes |
| 10 | **State Machine** | `transitions` table + `next(state,event)` + one `_state` | boolean soup | reliability | illegal event = no-op |
| 11 | **Robustness** | `res.ok` + `AbortController`/timeout + `try/catch` + `isConnected` | silent async failure | reliability | fetch only rejects on *network* fail |
| 12 | **Stateful Form** *(10+11)* | machine gates async; persistent `aria-live` | — | — | don't recreate the live region |
| 13 | **CRUD facade** | service `list/create/update/remove` over swappable backend | `fetch` scattered in UI | maintainability | optimistic update → reconcile → revert |
| 14 | **Routing** | `location.hash` + `hashchange` + routes map | nav state hidden from URL | usability | router owns the hash → anchor collisions |

**Compose-up:** building blocks = 1,2,4,5,7,8,10,11; capstones (combos) = 3,6,9,12; + 13,14.

---

## 7. High-level architecture patterns

- **Client-Server** — client (browser; untrusted, variable) · server (controlled, securable) · **network** (variable!). **Localhost Effect** = local dev hides real network problems.
- **Layered web model** — HTML (structure) / CSS (presentation) / JS (behavior).
- **Graceful degradation** (build full, fail *usefully*) vs **Progressive enhancement** (baseline works, layer JS on top).
- **MPA** (full page loads, robust) vs **SPA** (one page, JS swaps views, fast but fails w/o JS).
- **CRUD** = Create/Read/Update/Delete. Simple technically, hard *contextually* (what are you CRUDing?).
- **REST** — URLs = **nouns** (resources), HTTP methods = **verbs**. POST=create, GET=read, PUT/PATCH=update, DELETE=delete. APIs are UIs for developers.
- **MVC** — Model (data/rules) · View (UI) · Controller (bridge); Model & View never talk directly.

---

## 8. Code smells (with fix + -ility)

**JavaScript**
- `http://` → `https://` — **security**
- string-concat URL → `encodeURIComponent` — security/reliability
- no `res.ok` / no `try/catch` / no timeout → robustness — **reliability**
- `innerHTML` with user data → `textContent` — **security (XSS)**
- `==` → `===` — reliability
- object into `localStorage` without `JSON.stringify` — reliability
- `setInterval`/`setTimeout` never cleared; listener never removed → leak — maintainability/reliability
- `parseInt` without radix → add `10`
- magic numbers → named constants — **maintainability**
- vague names (`u`, `d`, `el`) → maintainability
- hardcoded `localStorage`/`fetch` → DI — testability/portability
- boolean soup (`isLoading && isSuccess`) → state machine — reliability
- one component poking another's internals → pub/sub — maintainability

**HTML**
- `<div class="nav">` → `<nav>` — **accessibility**/maintainability
- `<div onclick>` as a button → `<button>`/`<a>` — **accessibility** (keyboard)
- inline `onclick=` → wire in JS — maintainability/security
- `<img>` no `alt` → add `alt` — **accessibility**
- `<input type="text">` for email/password → right `type` — usability/**security** (mask password)
- placeholder used as label → real `<label>` — **accessibility**
- `<font>`, tables-for-layout → semantic + CSS — maintainability/accessibility
- "click here" link text → descriptive text — usability

**CSS**
- bad contrast (gray on gray) → **accessibility**
- fixed `font-size: 11px` → `rem` (scales) — accessibility/usability
- `!important` everywhere → maintainability
- bare element selectors (`div { }`) styling everything → use classes — maintainability
- *(note: a `1px` border is fine — px on font-size is the smell, not px everywhere)*

---

## 9. Smell → pattern → -ility (the master mapping)

| You see… | Pattern/fix | -ility |
|---|---|---|
| Component reaches into another's internals / calls it directly | **Pub/Sub** | maintainability |
| Listener on every child / re-bind each render | **Event Delegation** | performance/maintainability |
| Giant if/else picking interchangeable behavior | **Strategy** | maintainability |
| Hardcoded `localStorage`/`fetch`/one backend | **Dependency Injection** | testability/portability |
| Contradictory boolean flags | **State Machine** | reliability |
| `fetch` w/o `res.ok` / `try/catch` / timeout | **Robustness** | reliability |
| UI + data logic tangled | **MVC** | maintainability/testability |
| `fetch` calls scattered through UI | **CRUD facade** | maintainability |
| One huge component file | **Components/Decomposition** | maintainability |
| Nav state not in URL | **Routing** | usability |

---

## 10. Agile ↔ tooling

- **CI** (Continuous Integration) — integrate + test into a working build (internal confidence). **CD** (Continuous Delivery) — push that build to users (riskier).
- **Why CI/CD matters with Agile:** Agile = many small, safe, reversible steps; that's only safe with **fast automated feedback** (tests, lint, build) so failures are caught early and rollback is cheap → avoids the "**big ball of mud**" / rolling complexity uphill.
- **Why pull requests:** quality gate before `main`; catch design/bugs/missing tests; spread knowledge. **Large PRs → weak "lgtm" reviews** → keep PRs small.
- **Build-pipeline steps (typical order, fast→slow):** lint/format → unit tests → build/merge → integration/E2E → docs → optimize/bundle → deploy.
- **Feature flags/toggles** — ship work hidden behind a condition; enables trunk-based merging + A/B; a failed experiment is a flag flip.
- **A/B testing** — compare variants on real users; risk: more clicks ≠ value (metrics hide intent).
- **Merge conflict** — two branches edit the same lines; the merging PR resolves it; small frequent merges hurt less.
- **"Feel the hate before you automate"** — understand the manual pain first (avoid premature automation/YAGNI).

---

## 11. Testing

- **Testing pyramid** — many fast **unit** tests at the base → fewer **integration** → few slow **E2E/acceptance** at the top. Becomes a **"polygon"**: the right mix depends on the project's risks (some need more browser, load, security, pixel tests). **No checkbox thinking — test proportional to risk.**
- **Pure functions** (e.g. `strategies.byNameAsc`, `next(state,event)`) are easiest to unit-test; DOM is slow/fragile → push tests down to pure functions.
- **TDD** — **Red** (failing test) → **Green** (make it pass) → **Refactor**. Slow to start; matters more with LLM code (a verification boundary). 
- **BDD** — Given/When/Then tied to **user behavior** (fills TDD's "is this what the user wants?" gap).
- **Code coverage** — reveals untested code, but **executed ≠ well-tested** (a line can run with no assertion).
- **Security — golden rule:** you can't trust users → **trust no input or output**. **STRIDE**: **S**poofing, **T**ampering, **R**epudiation, **I**nformation disclosure, **D**enial of service, **E**scalation of privilege.
- **Acceptance testing** = the "final boss" — software can be technically correct and still rejected; reduce risk by getting interfaces to users early (Interface First).
- **Testing forms:** valid · empty · wrong type/format · boundary length · malicious input.

---

## 12. Operating & software lifecycle

- **DevOps = cultural** (joins dev + ops, breaks silos). **CI/CD = technical** pipeline. **Not the same thing.**
- **Availability:** **100% uptime is a passing moment, not a state.** Each "nine" costs non-linearly.
  - 99% ≈ 3.65 days/yr down · 99.9% ≈ 8.76 hr/yr · 99.99% ≈ 52.6 min/yr · 99.999% ≈ 5.26 min/yr.
  - High availability needs redundancy, monitoring/alerting, automated recovery, **chaos engineering**, on-call.
- **Monitoring:** **synthetic** (scripted bots, repeatable but miss real conditions) vs **RUM** (real users, real conditions, but privacy risk). **`200 OK` ≠ healthy** (error pages can return 200) → check content / a diagnostic route.
- **Logs** — essential but risky (huge, **PII**, attackers delete them). **Status page off the failing infra.**
- **Analytics** can mislead (more clicks may = confusion). **"No complaints ≠ correct"** (users silently leave).
- **Software is a garden, not a building** — tend it over time. **Birth → death lifecycle**: your choices (debt, coupling, missing tests, scattered knowledge) affect its long-term health.

---

## 13. Documentation

- **ADR (Architecture Decision Record)** — captures a decision, its context, alternatives, and **why** — for long-lasting, hard-to-reverse choices.
- **JSDoc** — generate docs **from the code**, keeping them next to the implementation → fights the "docs go stale" problem.
- General: keep a **single source of truth** in the repo; don't scatter knowledge across Slack/Drive/heads (low bus factor).

---

## 14. GenAI reasoning (expected on the exam)

- AI can produce code that **"looks correct while being wrong"** → tests/TDD become the verification boundary you trust.
- **Preservation / relocation of complexity:** AI doesn't make the hard part vanish — it **moves** it (hardcoding `localStorage` or omitting cleanup *looks* fine in a demo but becomes **tech debt** dumped on reviewers, maintainers, and users).
- **Shifting difficulty:** effort moves from **writing** code → **reviewing, verifying, owning** it. "More code = more bugs," so the bottleneck and risk shift to **critical evaluation** — the human's durable edge.
- AI carries **pattern bias** (force-fitting OOP where SQL/HTML/CSS/JSON fit better). Wrap AI/3rd-party code behind an **abstraction** (DI) so you can replace it — "once included, it's your responsibility."

---

## 15. Your team / project + your CI/CD pipeline

Be ready to describe **your own** project: patterns you used, an -ility that drove a decision, a smell you cleaned, a doc (ADR/JSDoc/README) you kept.

**Your group-23 pipeline (concrete instance of §10):**
- **`test.yml`** runs on **every PR** (all branches), **4 parallel** jobs: **ESLint** (style), **Jasmine** (unit), **Playwright** (E2E, Chromium), **html-validate + stylelint** (HTML/CSS). → quality gate before merge. *(This is CI.)*
- **`build.yml`** runs on **push to main/dev**: builds a **Docker image** (multi-arch) and pushes it to **GHCR** (tags: commit-SHA + latest). *(Build half of CD.)*
- **Deploy (decoupled, pull-based GitOps):** **Argo CD Image Updater** watches GHCR → new image → **Argo CD** deploys to the **Kubernetes** cluster (dev/prod). The image is the portable artifact — deployable "almost anywhere."
- Maps to the pipeline steps: lint → unit → E2E → validate → **build artifact** → deploy. Fast checks first; build/deploy after merge.

---

## 16. Priority + where to drill

**If time is short, master these three (they cut across the most questions):**
1. **Code → smell → -ility → fixing pattern** (the §9 mapping).
2. **The -ilities and their trade-offs** (§3, §4).
3. **CI/CD + testing pyramid + TDD/BDD** (§10, §11).

**Drill files in this folder:**
- `Practice-Midterm2` variants **1–3** (concept) · **4–6** (code review/refactor/pick-between)
- `CHEAT-SHEET.md` (terse fingerprints) · `Patterns-Located-In-Lessons.md` (where each pattern lives)
- `CODE-WALKTHROUGH.md` (plain-English tour of every lesson)
- `JS-FOUNDATIONS.md` (if reading code still feels shaky)

**Two reflexes to walk in with:**
- For any code: *"untrusted input? failure handled?"* (security + reliability) and *"what -ility does this help or hurt?"*
- For any design choice: *"appropriate, not correct — what does it trade away?"*
