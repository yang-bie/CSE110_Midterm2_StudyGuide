# CSE 110 Midterm 2 (Practice — Variant 2) — Powell Spring 2026

Name: ______________________________   Score: ______ / 120

PID: ______________________   Team #: ______

> Variant 2: **all-new questions**, same format and difficulty as Midterm 1. This set
> leans on the corners Variant 1 touched less — Operating & Evolving, Building & Deving,
> data formats, the security models, and the other design patterns (Event Delegation,
> Routing, CRUD facade, Robustness, Decomposition).
>
> Try the whole thing first; the **Answer Key** is at the bottom.

---

## Part A — Foundations, Requirements, Architecture

**1. [3 pts]** At a job interview you're asked to define **DevOps** in your own words as
precisely as possible. Be specific and terse — and make clear it is **not** simply
"CI/CD."

<br><br><br>

**2. [3 pts]** What was the point of the last question? Why would an interviewer want
you to separate a *cultural* practice (DevOps) from a *technical* one (CI/CD)? Should
you care about this distinction on your own team? Why?

<br><br><br>

**3. [3 pts]** "Software is greater than code." List **at least three** non-code
artifacts that are still part of the *software* a team produces.

1. ________________________
2. ________________________
3. ________________________

<br>

**4. [6 pts]** A teammate ships this hero image:

```html
<img src="hero.png">
```

…where `hero.png` is an un-optimized 4 MB file with no `alt`, no width/height, and the
image contains readable text. Discuss the **positive and negative** aspects using
**trade-off-oriented thinking**. Name at least two specific "-ilities" at risk and give
one better choice.

Positive:
<br><br>
Negative:
<br><br>
Better choice:
<br><br>

**5. [5 pts]** Your manager wants to use **code coverage percentage** as *the* quality
metric and reward developers for hitting 90%. Discuss the **pros and cons** of this from
multiple angles, including what happens when people "code to the grade."

<br><br><br><br>

**6. [3 pts]** Code review measurably improves quality, yet developers often resist it or
rubber-stamp PRs with an empty "lgtm." Why do you think this happens?

<br><br><br>

**7. [2 pts]** A team wants to add a popular third-party library chosen mainly because it
has the most **GitHub stars**. Fill in the table.

| | |
|---|---|
| **Positive Outcomes** | |
| **Negative Outcomes** | |

<br>

**8. [8 pts]** Fill in the table for each design **pattern**: what it **decouples / what
can change independently**, and the **code smell it avoids**. The first row is a model
answer.

| Pattern | What it decouples (what can change independently) | Code smell it avoids |
|---|---|---|
| *Pub/Sub* | *Publisher and subscriber communicate via events, so neither holds a hardcoded reference; either can change independently.* | *Component A directly calling Component B's methods.* |
| Event Delegation | | |
| Components / Decomposition | | |
| CRUD service facade | | |
| Routing | | |

<br>

**9. [3 pts]** Fill in the blanks (architecture catchphrases).

**DRY** reduces ___________________ ; **orthogonality** reduces ___________________
among components. **YAGNI** warns you not to add features/abstractions before you
___________________ .

<br>

**10. [3 pts] Knowledge Spreading:** Project knowledge often gets scattered across Slack,
Google Drive, issue trackers, wikis, and people's heads.

Definition (what's the risk): ____________________________________
<br>
Mitigation: ____________________________________________________
<br>
Possible Cause(s): _____________________________________________

<br>

**11. [12 pts] Architecture: Decomposition, Cohesion, Coupling.**

Good architecture aims for **high** ___________________ and **loose**
___________________ .

Applying a design pattern *before* you deeply understand both the problem and the
pattern is generally [ helpful ] [ harmful ] [ both ].

Explain: ________________________________________________________
<br><br>

A team splits a monolith into many tiny **microservices**. State one benefit and one
cost (what does over-decomposition trade away?):

Benefit: ________________________________________________________
<br>
Cost: ___________________________________________________________
<br>

Code-smell spotting — for the scenario below, name the smell **and** the pattern that
fixes it:

> "Every sort mode (`A–Z`, `Z–A`, newest, oldest) is a new branch in a giant
> `if/else` chain inside the list component."

Smell: __________________  Pattern: __________________

<br>

---

## Part B — Specifics: Data, Web, Git, Process [30 pts]

**12.** Short technical answers.

a) **[4]** Identify each data format and give one trade-off word for each:
   `1,true,"UCSD"` → ________ ;
   `{ "rank": 1 }` → ________ ;
   `<rank>1</rank>` → ________ ;
   indentation-based, popular in Rails → ________ .

b) **[2]** What is a **DSL** (spell it out), and one caution from the guides about
building one?

________________________________________________

c) **[3]** What is a `.gitignore` for, and name two things you'd typically put in it?

________________________________________________

d) **[2]** To get your branch reviewed *before* it merges into `main`, you open a
___________________ . If your new code collides with others' changes during a merge, you
get a ___________________ .

e) **[3]** URL case sensitivity — circle **insensitive** or **sensitive** for each:
   Protocol ( ins / sens )  Domain ( ins / sens )  Path/Filename ( ins / sens )

f) **[2]** Pick the right image format: a company **logo** → ________ ; a
**photograph** → ________ .

g) **[3]** Name two semantic HTML elements that can **reduce the need for custom
JavaScript**, and say why semantic markup is preferred over `<div>` soup.

________________________________________________

h) **[2]** What does **JSDoc** let you do, and how does that fight stale documentation?

________________________________________________

i) **[3]** Client-side validation vs server-side validation: which is for **UX**, which
is for **security**, and why is client-side validation *alone* unsafe?

________________________________________________

j) **[3]** What is **chaos engineering**, and name one thing **high availability**
requires besides it.

________________________________________________

k) **[2]** Write the git command to initialize a repo in the current directory, and the
command to stage a file named `index.html`.

________________________________________________

l) **[2]** A teammate suggests committing the `node_modules/` folder into the repo. Give
one reason that's usually a bad idea.

________________________________________________

---

## Part C — Diagram, Table, AI, Scenario

**13. [9 pts]** A typical **MVC** data flow is shown below for reference.

```
   user input
       |
       v
   [ View ] --- "intent" event ---> [ Controller ] --- calls ---> [ Model ]
       ^                                                              |
       |------------------ update / change event ---------------------|
```

a) **[3]** What is *good* about separating an app this way? (What can change
independently?)

<br><br>

b) **[3]** What is a typical complaint, or why does MVC become complicated **on the
web** specifically?

<br><br>

c) **[3]** Is MVC the exact right structure for *every* problem? **Y / N** — explain,
otherwise write N/A.

<br><br>

**14. [10 pts]** Briefly explain each **Operating & Evolving** concept. Don't just
describe it — also give the **value/reason** or the **risk** it carries.

| Concept | Description | Value / Reason (or Risk) |
|---|---|---|
| DevOps vs CI/CD | | |
| Logging | | |
| Synthetic access check (`200 OK`) | | |
| Real User Monitoring (RUM) | | |
| "Software is a garden, not a building" | | |

<br>

**15. [10 pts] AI-driven SWE.**

a) **[3]** AI code generators tend to over-apply **familiar patterns** (e.g. forcing OOP
onto a problem). Explain this *pattern bias* and give one example where a declarative
tool (SQL, HTML, CSS, JSON) is more appropriate than OOP.

<br><br>

b) **[3]** Why does wrapping a dependency (or AI-suggested library) in an **abstraction /
insulation layer** make your system safer? Tie it to a pattern from the guides.

<br><br>

c) **[4]** "More code = more bugs" (the joke `E = MC²`). AI can generate *a lot* of code
fast. Using **trade-off thinking**, explain the quality risk this creates and give one
concrete mitigation. Bonus for naming a specific technique.

<br><br>

**16. [10 pts] The 3 a.m. Page:** You're on-call at *GardenPath Apps*. The founder,
**Ava Uptime**, promised customers **"100% uptime"** in the contract. At 3 a.m. your
synthetic monitor is happily reporting `200 OK`, but users are tweeting that checkout is
broken.

a) **[6]** Explain **(i)** why promising 100% uptime is an engineering mistake, and
**(ii)** why a `200 OK` check gave false comfort here — what *better* check should they
have had? Then name the **metaphor** the guides use for *operating* software over time.

(i): ____________________________________________________________
<br>
(ii) + better check: ____________________________________________
<br>
Metaphor: _______________________________________________________

b) **[4]** Using **trade-off thinking**: where should the **status page** be hosted, and
why? Give **one** defensive operations practice (e.g. RUM, multi-location checks,
diagnostic route, chaos engineering) that would have caught this sooner. Bonus for
saying why "no complaints" wouldn't have proven the app was fine.

<br><br><br>

---
---

# ANSWER KEY

> Model answers — key points a grader looks for. Wording can differ.

**1.** DevOps is a **cultural** approach that joins the **creation and operation** of
software and breaks down the old dev-vs-ops silo (admins wanted stability, developers
wanted change). Because developers understand what they built, they take a heavier hand
in running/observing it. It is **not** CI/CD: CI/CD is the *technical* pipeline for
building/integrating/delivering; DevOps is the org culture around it. (Warning: some
orgs abuse "DevOps" to make devs do more with fewer resources.)

**2.** It checks whether you grasp that process/culture ≠ tooling. Buying a CI/CD tool
doesn't give you DevOps if the silos and incentives don't change. On your own team it
matters because mislabeling a *cultural* problem as a *tooling* one means you "fix" it by
adding tools and the real coordination problem remains.

**3.** Any three: design documents, diagrams, user stories, meeting notes,
presentations, messages, **tests**, documentation, **team knowledge**. (Strong answer:
these preserve intent, coordinate the team, and help future maintainers.)

**4.** *Positive:* one tag, quick, no processing step. *Negative / ilities:*
**Accessibility** (no `alt`; text-in-image is unreadable to screen readers / low
contrast), **Performance** (4 MB un-optimized, no dimensions → slow load + layout
shift), **Usability**, **Maintainability**. *Better choice:* add meaningful `alt`, set
width/height, optimize/compress (TinyJPG-style, automate in CI/CD), use the right format
(JPEG/WebP/AVIF for a photo), and use `<picture>`/`srcset` for responsive delivery;
prefer real text over text baked into an image.

**5.** *Pros:* coverage reveals **untested areas**, is easy to measure and automate,
gives a baseline. *Cons:* **executed ≠ well-tested** — a test can run a function without
asserting meaningful behavior or edge cases (e.g. `validateEmail("x")` bumps coverage
but never checks valid/empty/malicious input). Rewarding a number makes people **"code
to the grade"**: write shallow tests, chase the % instead of real risk. Better: test
**proportional to risk**, value assertions and edge cases over the headline number.

**6.** Manual review is **socially hard** — people dislike giving/receiving criticism, so
reviews decay into empty "lgtm." Teams are often rewarded for shipping, not reviewing;
**large PRs** make real review impractical, so reviewers skim. (Fixes: smaller PRs,
specific feedback about design/clarity/maintainability, automated checks so humans focus
on judgment.)

**7.** *Positive:* may genuinely be best-of-breed, save dev time, add capability. *Negative:*
**popularity ≠ engineering quality**; a dependency **becomes part of your system and your
responsibility**; it has its own dependencies (fragility, conflicts, circular refs);
adds onboarding/integration cost. (Wrap it in an abstraction so you can replace it; "go
beyond Hello World" before trusting it.)

**8.** Sample answers:

| Pattern | Decouples / changes independently | Code smell avoided |
|---|---|---|
| Event Delegation | One listener on the **parent** handles many children via bubbling; children can be added/removed dynamically without rebinding. | A separate click listener on every child; rebinding on every re-render. |
| Components / Decomposition | Logic / template / styles / i18n split into separate modules; one concern changes without touching the others. | One huge component file mixing logic, HTML, CSS, text, and handlers. |
| CRUD service facade | Component talks to a stable `list/create/update/remove` service; the **backend** (memory today, REST later) can change underneath. | `fetch` calls scattered through the UI; UI tightly coupled to one backend. |
| Routing | The **URL hash** is the source of truth for the active view; the active page changes by changing the URL. | Hidden nav state not reflected in the URL; scattered show/hide conditionals. |

**9.** DRY reduces **duplication**; orthogonality reduces **interdependence
(coupling)** among components; YAGNI: don't add it before you **need it**.

**10.** *Risk/definition:* when knowledge lives scattered (or only in people's heads),
it becomes **hard to find, hard to update, and easy to lose** — the project becomes
fragile (low bus factor). *Mitigation:* keep a **single source of truth** — put plans,
diagrams, user stories, tests, and docs **in the repo (or repo wiki)** or link them from
it; review doc updates in PRs; generate docs from code (JSDoc). *Causes:* "best-of-breed"
tool sprawl, no clear owner, rewarding features over documentation, plans kept far from
code.

**11.** High **cohesion**, loose **coupling**. Applying a pattern before understanding it
is **harmful** (it becomes a crutch / force-fits the problem; "don't apply a pattern
until you deeply understand both the problem and the pattern"). *Microservice benefit:*
focused responsibility, independent deploy/scaling. *Cost:* **network/communication
overhead, orchestration complexity, more failure points** — over-decomposition can even
*increase* coupling. *Smell:* giant conditional for interchangeable behavior →
**Strategy** pattern.

**12.**
- a) `1,true,"UCSD"` = **CSV** (compact/bulky-free but weakly typed/ambiguous);
  `{ "rank": 1 }` = **JSON** (fashionable, robust, human-readable-ish);
  `<rank>1</rank>` = **XML** (verbose/heavy, was once fashionable);
  indentation-based = **YAML** (human-readable, popular in Ruby/Rails, whitespace-fragile).
  *(Key idea: choose by appropriateness, not fashion; new ≠ better.)*
- b) **Domain Specific Language** — caution: give yourself time to do it right and
  **document it strictly**.
- c) `.gitignore` tells git which files **not to track**; e.g. `.DS_Store`, build/
  generated files, `node_modules/`, secrets/`.env`.
- d) a **pull request**; a **merge conflict**.
- e) Protocol = **insensitive**, Domain = **insensitive**, Path/Filename = **sensitive**
  (may be). *(Query strings: may be sensitive too.)*
- f) Logo → **SVG**; photograph → **JPEG / WebP / AVIF**.
- g) e.g. `<dialog>` and `<details>` (also `<nav>`, `<form>`); semantic HTML is
  **accessible by default**, more maintainable, and lets the platform do work you'd
  otherwise re-build in JS — avoid making everything a `<div>`.
- h) JSDoc-style comments **generate documentation from the code itself**, keeping docs
  *near* the implementation so they go stale less often.
- i) **Server-side = security** (client-side can be bypassed), **client-side = UX**
  (instant feedback). Client-side alone is unsafe because an attacker can skip the
  browser entirely and post straight to your server.
- j) **Chaos engineering** deliberately breaks things in production to test resilience.
  High availability also needs **redundancy at every layer, monitoring/alerting,
  automated recovery, on-call rotations, incident response**.
- k) `git init` ; then `git add index.html`.
- l) `node_modules/` is large/generated and rebuildable from the lockfile; committing it
  bloats the repo and invites accidental/conflicting dependencies — use `.gitignore`.

**13.**
- a) Separation of concerns: **Model** owns data/rules (DOM-free, testable, reusable),
  **View** shows UI + emits intent, **Controller** bridges them. Model logic can change
  without rewriting the view, and vice-versa.
- b) On the web there's a **network boundary** plus choices about client vs server
  computation, where state/DB live, and how rendering happens — so MVC sprouts **many
  variations** and extra complexity.
- c) **N.** Patterns are tools, not laws; some problems don't model well as
  objects/MVC and are better served by **declarative tools (SQL, HTML, CSS, JSON DSLs)**.
  Match complexity to the actual problem and audience.

**14.** Sample answers:

| Concept | Description | Value / Reason (or Risk) |
|---|---|---|
| DevOps vs CI/CD | DevOps = **cultural** joining of dev + ops; CI/CD = **technical** build/integrate/deliver pipeline. | Don't confuse a culture change with buying a tool; CI/CD doesn't create DevOps. |
| Logging | Records enough to understand what happened during an incident/time window. | Essential for debugging/ops — but **risky**: huge volume, cognitive load, **PII**, and attackers may delete logs. |
| Synthetic `200 OK` check | A bot pings the site and checks the HTTP status. | Catches gross downtime, but gives **false comfort** — an error page can still return `200 OK`. Better: check for expected content/assets or a diagnostic route. |
| RUM | Observes **real users** in real conditions (perf, nav timing, JS errors, session replay). | Catches what synthetic checks miss; risk = it can become **privacy-invasive**. |
| Garden, not building | A building is built once and left; software must be continuously tended (observe, prune, plant, adjust). | Sets the expectation that software **degrades and evolves** and needs ongoing operational care. |

**15.**
- a) Generated code carries **pattern bias**: it reaches for familiar (often OOP)
  solutions even when they don't fit. Example: representing tabular data + queries is
  better done in **SQL**; page structure in **HTML/CSS**; config in a **JSON/YAML DSL** —
  wrapping these in object hierarchies adds bloat for no gain.
- b) An **insulation/abstraction layer** (Dependency Injection / a service facade) means
  your code depends on an **interface**, not the concrete library — so you can swap,
  test, or replace the dependency (or the AI's chosen one) without rewriting the rest.
  A dependency "becomes part of your system," so isolating it limits the blast radius.
- c) More code/complexity = more surface area for bugs, and "**more**" also means more
  dependencies, services, config, and integration points. AI lets you generate volume
  fast, so the risk is **unverified, plausible-looking code** piling up. Trade-off: speed
  now vs. debugging/maintenance later. Mitigation: **proportional testing**, **TDD**
  (Red-Green-Refactor) as a verification boundary, code review, and **minimalism** (KISS/
  YAGNI — don't generate what you don't need).

**16.**
- a) **(i)** 100% uptime is **not a stable state, only a passing moment** — hardware,
  networks, config, and bugs all eventually fail; promising it in a contract is an
  engineering (and legal) mistake. Better to pick an availability level that **serves
  users while staying economically sustainable**. **(ii)** The `200 OK` check is a
  **surface check that gives false comfort** — an error/checkout-broken page can still
  return `200 OK`. Better: fetch the page and **assert expected content/assets**, hit a
  **diagnostic route**, or run a **scripted scenario check** that actually completes a
  checkout. **Metaphor:** software is a **garden, not a building** (tend it over time).
- b) The **status page must NOT be hosted on the same infrastructure that's failing** —
  otherwise it goes down with the app. *Defensive practices:* scripted scenario/E2E
  synthetic checks that complete real workflows, **multi-location** checks, **RUM** to
  see real failures, a monitored **diagnostic route**, and **chaos engineering** to
  surface these failure modes before customers do. *"No complaints" wouldn't prove
  health:* users may **silently leave**, lack time to report, or not know how — silence
  has friction, so it's weak evidence of correctness.

---

*Coverage: Operating & Evolving (DevOps vs CI/CD, availability/100% uptime, logging,
synthetic vs RUM, 200-OK trap, garden metaphor) • Building & Deving (software > code,
dependencies, .gitignore, branches, PRs, JSDoc, semantic HTML, image/data formats,
client vs server validation) • Architecture (DRY/orthogonality, YAGNI/KISS, cohesion/
coupling, decomposition, microservices, pattern danger) • In-class Architecture (DSL,
data-format fashion vs foundation, MVC on the web, declarative vs OOP) • Patterns (Event
Delegation, Decomposition, CRUD facade, Routing, Strategy, DI) • Testing & Quality (code
coverage, proportional testing, TDD, "E=MC²", chaos engineering).*
