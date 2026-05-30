# CSE 110 Midterm 2 (Practice) — Powell Spring 2026

Name: ______________________________   Score: ______ / 120

PID: ______________________   Team #: ______

> This practice exam mirrors the **structure and question style** of Midterm 1
> (define-in-your-own-words, "why did I ask that", lists, trade-off discussions,
> fill-in-the-blanks, concept tables, diagram analysis, technical syntax, and a
> named-character scenario). The **content** is drawn entirely from the Midterm 2
> study guides: Requirements/Specifications, Architecture, In-class Architecture,
> Patterns, Building & Deving (1 & 2), Operating & Evolving, and Testing & Quality.
>
> Answer in the space provided. An **Answer Key** is at the bottom — try the whole
> thing first, then grade yourself.

---

## Part A — Foundations, Requirements, Architecture

**1. [3 pts]** At a job interview you've been asked to define **Software Architecture**
in your own words as precisely as possible. Be specific but terse.

<br><br><br>

**2. [3 pts]** What was the point of the last question? In other words, why would an
interviewer ask you to define *architecture* rather than just asking you to code
something? Should *you* care about this distinction in your own projects? Why?

<br><br><br>

**3. [3 pts]** Functional suitability ("does it do the right thing") is one quality
attribute, but there are many others. List **at least three** non-functional
"-ilities" from the 8 key ISO 25010 categories.

1. ________________________
2. ________________________
3. ________________________

<br>

**4. [6 pts]** A teammate adds this line to your page:

```html
<link rel="stylesheet" href="http://cdn.someblog.example/themes/cool.css">
```

Discuss the **positive and negative** aspects of this decision using
**trade-off-oriented thinking**. Name at least two specific "-ilities" that are at
risk and suggest one better design choice.

Positive:
<br><br>
Negative:
<br><br>
Better choice:
<br><br>

**5. [5 pts]** Many organizations brag about "five nines" of availability, and some
chase 100% uptime. Discuss the **pros and cons** of using availability ("nines") as
your reliability metric and of chasing ever-higher uptime. Great answers think about
this from multiple angles, including cost. (Recall: 99.9% ≈ 8.76 hrs/yr down;
99.999% ≈ 5.26 min/yr down.)

<br><br><br><br>

**6. [3 pts]** It is widely held that *planning is cheaper than coding* — a sketch,
wireframe, or user story is easier to revise than code. Despite this, many developers
still resist doing requirements/planning work up front. Why do you think that is?

<br><br><br>

**7. [2 pts]** A team is tempted to build a fully-automated, professional-grade build
pipeline (linting, formatting, optimization, doc generation, multi-stage deploy)
*before writing any real product code*. Fill in the table.

| | |
|---|---|
| **Positive Outcomes** | |
| **Negative Outcomes** | |

<br>

**8. [8 pts]** Briefly fill in the table for each design **pattern**. State what the
pattern **decouples / what can now change independently**, and the **code smell it
avoids**. The first row is a model answer for reference.

| Pattern | What it decouples (what can change independently) | Code smell it avoids |
|---|---|---|
| *Pub/Sub* | *Publisher and subscriber communicate through events, so neither needs a hardcoded reference to the other; either side can change independently.* | *Component A directly calling methods on Component B (tight coupling).* |
| Strategy | | |
| Dependency Injection | | |
| State Machine | | |
| MVC | | |

<br>

**9. [3 pts]** Fill in the blanks.

When making architecture decisions, a key SWE tip is to notice the word
___________________ as opposed to "correct." There is rarely a perfect answer; you are
balancing ___________________. The mindset we should fight "at all times" is
___________________ -first thinking.

<br>

**10. [3 pts] The Big Ball of Mud / Rolling Complexity Uphill:** Software teams may face
the challenge of complexity that grows until the project becomes unmaintainable.

Definition: ____________________________________________
<br>
Mitigation: ____________________________________________
<br>
Possible Cause(s): _____________________________________

<br>

**11. [12 pts] Requirements & User-Centered Thinking:**

A key tenet of requirements work is that vague human needs must be translated into
clear, ___________________ requirements. Asking a stakeholder directly for those needs
can be [ misleading ] [ helpful ] [ both ].

Explain: ________________________________________________
<br><br>

A startup keeps its requirements measurable. Rewrite the weak requirement below into a
**concrete, testable** one with measurable acceptance criteria:

> Weak: *"The dashboard should be fast."*

Stronger: ________________________________________________
<br><br>

We also distinguish the **system model** from the **user model**. Briefly define each,
and give one "smell" that suggests they are *misaligned*.

System model: __________________________________________
<br>
User model: ____________________________________________
<br>
Misalignment smell: ____________________________________

<br>

---

## Part B — Specifics: Tools, Web, Patterns, Process [30 pts]

**12.** Short technical answers.

a) **[2]** What does the acronym **CRUD** stand for? C ___ R ___ U ___ D ___

b) **[2]** In **REST**, URLs behave like ___________ (parts of speech) and HTTP
methods behave like ___________.

c) **[3]** Match each HTTP method to its CRUD-style action:
`POST` → ________  `GET` → ________  `PUT/PATCH` → ________  `DELETE` → ________

d) **[3]** Markdown is common in SWE; **HTML** is used when you need more. Write a
primary heading that says "HTML **is** *useful*!" using valid, semantic HTML.

________________________________________________

e) **[3]** Write the **CSS** rule (selector + rule only, not a whole document) to make
that `h1` have white text on a dark-blue background.

________________________________________________

f) **[3]** Rewrite this `<div class="nav">...</div>` to use **semantic HTML**, and say
one "-ility" it improves by default.

________________________________________________

g) **[2]** You finished a small feature. Write the git commands to (1) stage the file
`feature.js` and (2) commit it.

________________________________________________

h) **[3]** A teammate's commit message is `Small fixes`. Why is this a bad commit
message, and write a better one.

________________________________________________

i) **[3]** Compare **CI** and **CD** in one sentence each, and say which is riskier and
why.

CI: ____________________________________________
<br>
CD: ____________________________________________
<br>
Riskier: _______________________________________

j) **[2]** What is a **feature flag** (feature toggle), and what is it used for?

________________________________________________

k) **[2]** "Feel the hate before you automate." What does this mean, and what failure
mode does it protect against (name the acronym)?

________________________________________________

l) **[2]** Client-server always has three pieces. Name all three.

________________________________________________

---

## Part C — Diagrams, Tables, Process, Scenario

**13. [9 pts]** The **Testing Pyramid** is shown below for reference.

```
              /\
             /  \      User Acceptance / E2E   (few, slow, broad)
            /----\
           /      \    Integration tests
          /--------\
         /          \  Unit tests              (many, fast, cheap)
        /------------\
```

a) **[3]** What is *good* about this model? Why is it shaped like a pyramid?

<br><br>

b) **[3]** What is a typical complaint, or a reason this becomes a "testing
**polygon**" instead of a clean pyramid?

<br><br>

c) **[3]** Is this the exact correct test mix for *every* project? **Y / N** — explain
your answer, otherwise write N/A.

<br><br>

**14. [10 pts]** Briefly explain each Testing/Quality & Operations concept in the table.
Do not just describe it — also explain the **value/reason** it provides the team or
project.

| Concept | Description | Value / Reason |
|---|---|---|
| TDD (Red, Green, Refactor) | | |
| Code coverage | | |
| STRIDE | | |
| Real User Monitoring (RUM) | | |
| Acceptance testing | | |

<br>

**15. [10 pts] AI-driven SWE.** The industry realignment around AI code generation is
underway. Answer the following.

a) **[3]** Generated code can "look correct while being wrong." Explain how **TDD**
gives you protection here, and why this matters *more* with LLM code generation.

<br><br>

b) **[3]** Some engineers promote **specification-driven development**: write a highly
detailed spec, have AI build to it, and when you need changes you edit the spec and
regenerate. Compare this to pre-AI approaches and say one thing needed to make it work
well.

<br><br>

c) **[4]** The study guides repeatedly warn against **"solution-first thinking"** and
say *critical reasoning is the main thing humans still have over code generators*.
Using trade-off thinking, explain why blindly accepting AI-generated code is risky even
when it runs. Give a specific example of a hidden compromise it might contain.

<br><br>

**16. [10 pts] Facing the Network:** You just became Lead Engineer at *DriftDB*, a
startup whose founder, **Chip Localhostings**, vibe-coded a slick prototype over the
weekend. It runs *flawlessly* on his laptop and he wants to ship to thousands of public
users **tomorrow**. Bring some SE sanity to this.

a) **[6]** Fill in the **client-server** diagram with the three pieces and label which
side is *more under your control* and which is *possibly hostile / highly variable*.

```
   [   ?   ]  <------ (   ?   ) ------>  [   ?   ]
       |                                     |
  control? ____                         control? ____
```

Also: name the **bold-named effect** that explains why "works on my laptop" is
dangerous here.

Effect: ________________________________________

b) **[4]** Chip says "it worked in dev, so it's reliable." Using **trade-off thinking**,
explain what's wrong with that reasoning and give **one specific, defensive** thing the
team should do before exposing this to the public network. Bonus points for grounding
it in a concrete technique from the guides.

<br><br><br>

---
---

# ANSWER KEY

> Model answers below. These show the key points a grader would look for — your wording
> can differ as long as you hit the ideas.

**1.** Software architecture is the set of **significant / important design decisions**
that shape a system — especially the ones that are **hard or costly to change later**
(Booch's framing: significance = cost of change; Wilson adds structure, behavior,
tradeoffs, and effects on maintainability/scalability). It happens whether you plan it
or not; do it thoughtlessly and you still get an architecture, just an accidental/bad
one.

**2.** The point is to test whether you understand that SWE is about **decisions and
trade-offs**, not just typing code. Architecture is the "important + hard-to-change"
layer, so getting it wrong is expensive. Yes, you should care in your own projects:
naming the hard-to-reverse decisions early (and recording them in **ADRs**) is what
separates engineering from "vibe coding."

**3.** Any three of the 8 ISO categories (besides functional suitability):
**performance efficiency, compatibility, usability, reliability, security,
maintainability, portability**. (The 17 "-ilities" list — scalability, availability,
modularity, etc. — also counts.)

**4.** *Positive:* quick to add, no need to write/host the CSS yourself, can reuse a
shared theme. *Negative / ilities at risk:* **Reliability/Availability** (if the remote
host is down the page renders unstyled), **Performance** (external, render-blocking
request), **Security** (plain `http://` can be modified in transit; third-party CSS is a
privacy/security risk), **Maintainability** (the file can change outside your repo and
break your design), **Compatibility** (unsupported CSS varies by browser). *Better
choice:* use **HTTPS**, host/pin critical CSS locally, add **Subresource Integrity**,
provide fallback base styles, and test under slow/offline conditions.

**5.** *Pros:* availability is a concrete, comparable number tied to user-facing
reliability; targets force investment in redundancy, monitoring, and recovery. *Cons:*
**100% uptime is not a stable state — only a passing moment**; everything eventually
fails (hardware, networks, config, bugs). Each extra "nine" costs **non-linearly** more
(redundancy at every layer, alerting, automated recovery, chaos engineering, on-call).
The better question isn't "how do we get perfect uptime" but **"what availability serves
user needs while staying economically sustainable"** — a risk/values question, not a
pure tech one.

**6.** Coding *feels* more productive than planning, so planning gets skipped. But
**skipping requirements doesn't remove them — it just hides them in someone's head**
until the software fails to meet them. Organizational incentives often reward shipping
features over docs/reliability, developers may not be trained in modeling notation, and
plans go stale, so people stop trusting them.

**7.** *Positive:* catches problems early, makes small experiments safe/fast, consistent
quality, fast feedback. *Negative:* **premature/over-engineered automation (YAGNI)** —
yak-shaving on a pipeline you don't need yet, added complexity before you understand the
real pain points. (Guide: "feel the hate before you automate"; grow the pipeline
incrementally.)

**8.** Sample answers:

| Pattern | Decouples / changes independently | Code smell avoided |
|---|---|---|
| Strategy | Behavior is selected at runtime from interchangeable functions sharing one interface; new behavior added without touching the caller. | Giant `if/else`/`switch` chains; hardcoded behavior in the component. |
| Dependency Injection | Component depends on an abstraction (e.g. a storage interface) passed in, not a concrete impl; the implementation can be swapped/tested. | Hardcoded backend (e.g. `localStorage` used directly everywhere). |
| State Machine | Valid states + transitions are explicit in a table; async flow changes by editing transitions. | Conflicting boolean flags (`isLoading`+`isSuccess` true at once) / impossible states. |
| MVC | Model owns data/rules, View shows UI + emits intent, Controller bridges them; model can change without rewriting the view (and vice-versa). | UI mutating state everywhere; business logic in DOM handlers; model depending on HTML. |

**9.** **appropriate** … **trade-offs** … **solution**-first thinking.

**10.** *Definition:* as a project grows, **complexity grows** and, without backstops,
the system degrades into a **"big ball of mud"** — the "rolling complexity uphill"
metaphor where the bundle can roll back and crush the team. *Mitigation:* move in
**small, safe, reversible steps** with backstops — tests, linting, CI, automation,
clean-as-you-go. *Cause(s):* "rocket-launch" commits, no tests/automation, skipping
refactoring/cleanup, rewarding features over maintenance → technical debt.

**11.** Blank: **clear, testable / precise** requirements; asking stakeholders directly
is **both** (they know their needs but speak vaguely, and may ask for things that aren't
actually beneficial). *Explain:* engineers build software for others, so user input is
essential — but vague human input produces vague/wrong software (like a vague LLM
prompt), so the engineer must interview, observe, prototype, and define acceptance
criteria. *Rewrite example:* "The dashboard should load in **under 1 second for 90% of
requests** on supported devices/networks, verified by performance testing." *System
model* = the developer's internal view (DB tables, services, classes, APIs). *User
model* = the user's view (tasks, goals, language, workflows). *Misalignment smell:*
navigation/labels mirror **database table names** instead of user tasks; UI uses
internal engineering jargon; one user goal requires jumping across unrelated screens.

**12.**
- a) **C**reate, **R**ead, **U**pdate, **D**elete.
- b) URLs behave like **nouns**; HTTP methods behave like **verbs**.
- c) `POST` → **Create**, `GET` → **Read**, `PUT/PATCH` → **Update**, `DELETE` →
  **Delete**.
- d) `<h1>HTML <strong>is</strong> <em>useful</em>!</h1>` (semantic `<strong>`/`<em>`,
  not `<b>`/`<i>`).
- e) `h1 { color: white; background-color: darkblue; }`
- f) `<nav>...</nav>` — improves **accessibility** (semantic markup is accessible by
  default; also helps maintainability/SEO).
- g) `git add feature.js` then `git commit -m "..."` (e.g.
  `git commit -m "[Feature] Add feature.js"`).
- h) `Small fixes` says nothing about *what* or *why*, doesn't support changelog
  generation or future understanding. Better:
  `[ Bug ] Added data sanitization to configuration screen #3455`.
- i) **CI** = continuously integrate/test code into a workable build (internal
  confidence). **CD** = automatically deliver that build out to production/users.
  **CD is riskier** because it directly affects real users.
- j) A **feature flag** hides new functionality behind a condition so it ships to a
  small subset of users; lets you experiment/observe stability before full rollout
  (still must be safe).
- k) Understand a manual pain point **before** automating it; protects against premature
  automation / **YAGNI** ("You Ain't Gonna Need It").
- l) **Client**, **Server**, **Network**.

**13.**
- a) Many **fast, cheap, focused unit tests** at the base, fewer slow/broad tests on
  top. Small tests run often, are cheaper, and easier to maintain; big E2E tests are
  slow and fragile — so you want lots of the former and few of the latter.
- b) The *right* mix depends on the project's risks, so it becomes a **"testing
  polygon"**: some projects need more browser/compatibility, load, security, pixel, or
  acceptance tests. Avoid **checkbox thinking**.
- c) **N.** Test mix should be **proportional to risk** — a life-critical system needs
  far more testing than a casual game feature; a web app may be heavy on E2E/browser
  tests, a high-traffic app heavy on load tests.

**14.** Sample answers:

| Concept | Description | Value / Reason |
|---|---|---|
| TDD | Write failing tests first (**Red**), write code to pass (**Green**), then **Refactor** while green. | Forces a verification boundary before code exists; produces better-specified, testable code (esp. valuable vs. AI-generated code). |
| Code coverage | Measures how much code is executed by tests. | Reveals untested areas — but executed ≠ well-tested; a line can run without asserting meaningful behavior/edge cases. |
| STRIDE | Threat categories: Spoofing, Tampering, Repudiation, Information disclosure, Denial of service, Escalation of privilege. | Gives a structured checklist for security testing, rooted in "trust no input/output." |
| RUM | Observes **actual users** in real conditions (perf, nav timing, JS errors, session replay). | Catches issues synthetic bots miss; but can become invasive — balance with privacy. |
| Acceptance testing | Checks whether **users accept** the delivered software. | The "final boss": software can be technically correct yet still fail if users reject it; reduce risk by getting interfaces to users early. |

**15.**
- a) Generated code can pass a glance but be wrong; **tests written first define the
  expected behavior** and fail loudly when the implementation is wrong, creating a
  verification boundary *before* you trust generated code. It matters more with LLMs
  because their output is plausible-looking and inconsistent (the "run a prompt 50
  times, get 50 answers" lesson).
- b) Pre-AI, a human wrote/debugged and added features manually; spec-driven dev makes a
  **detailed specification the source of truth** that the AI builds from, so you adjust
  the *spec* and regenerate. To work well you need a **highly detailed, precise spec**
  (vague spec → vague/wrong software, same as vague requirements/prompts) plus strong
  tests/acceptance criteria to verify the output.
- c) Running ≠ correct or appropriate. AI may bake in **hidden compromises**: an
  inappropriate pattern (OOP forced onto a problem that fits SQL/declarative tools),
  premature optimization/abstraction, a dependency you now own, security holes
  (untrusted input), or poor performance trade-offs you didn't choose. Engineering
  judgment — requirements, context, **trade-off awareness** — is the human edge;
  "appropriate, not correct," and fight solution-first thinking.

**16.**
- a) Diagram: `[ Client ] <--- ( Network ) ---> [ Server ]`. **Client** = the requester
  (often a web browser); rarely under your control, **possibly hostile**, highly
  variable. **Server** = services the request; **more under your control**, securable,
  more predictable capacity but **network-constrained**. The bold-named effect is the
  **"Localhost Effect"** — local dev hides real network variability and makes the web
  look like a happy path.
- b) "Works in dev" is the Localhost Effect: localhost has no real latency, packet loss,
  hostile inputs, or load, so reliability there says little about the wild public
  network — and trading away defensiveness for speed-to-ship is a bad trade at thousands
  of users. *Defensive things to do:* build for an unreliable/slow network (timeouts,
  **retries**, **AbortController** to cancel slow fetches, `try/catch`, check
  `res.ok` since fetch only rejects on network-level failure), distrust all inputs
  (the **Golden Rule of Web Security**), add monitoring/synthetic checks from multiple
  locations, do a **limited/beta rollout or feature flag** instead of full launch, and
  test under slow/offline conditions before exposing it publicly.

---

*Topics covered: Requirements & Specifications (5W1H, "-ilities", ISO 25010, vague→
concrete, system vs user model) • Architecture (significant/hard-to-change, ADRs,
appropriate vs correct, trade-offs, cohesion/coupling, abstraction) • In-class
Architecture (solution-first, fashion vs foundation, client-server, Localhost Effect,
CRUD, REST, MVC) • Patterns (Pub/Sub, Strategy, DI, State Machine, MVC, Robustness) •
Building & Deving (build pipeline, CI/CD, feature flags, small experiments, semantic
HTML/CSS, commit messages, "feel the hate before you automate") • Operating & Evolving
(availability nines, Localhost Effect, monitoring/RUM, garden metaphor) • Testing &
Quality (proportional testing, testing pyramid/polygon, TDD, STRIDE, acceptance
testing, code coverage).*
