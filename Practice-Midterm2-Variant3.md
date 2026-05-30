# CSE 110 Midterm 2 (Practice — Variant 3) — Powell Spring 2026

Name: ______________________________   Score: ______ / 120

PID: ______________________   Team #: ______

> Variant 3: **all-new questions** again. This set leans into security (golden rule,
> STRIDE, the three security models), Building & Deving web/JS specifics, Agile
> iteration, architecture catchphrases, and the **State Machine** and **Robustness**
> patterns.
>
> Try the whole thing, then check the **Answer Key** at the bottom.

---

## Part A — Foundations, Requirements, Architecture

**1. [3 pts]** At a job interview you're asked to define **technical debt** in your own
words as precisely as possible — and to contrast it with **technical investment**.

<br><br><br>

**2. [3 pts]** What was the point of the last question? Why frame engineering shortcuts
as "debt" at all, and should *you* track it on your own team? Why?

<br><br><br>

**3. [3 pts]** Security testing starts with distrust. List **at least three** of the six
**STRIDE** threat categories.

1. ________________________
2. ________________________
3. ________________________

<br>

**4. [6 pts]** A teammate drops this into the `<head>` to add analytics:

```html
<script src="http://tracker.example/v1/analytics.js"></script>
```

Discuss the **positive and negative** aspects using **trade-off-oriented thinking**.
Name at least two specific "-ilities" at risk and give one better choice.

Positive:
<br><br>
Negative:
<br><br>
Better choice:
<br><br>

**5. [5 pts]** A PM wants to optimize the product for **clicks / engagement** and treats
"more clicking" as proof users love it. Discuss the **pros and cons** of using
engagement clicks as your success metric, from multiple angles.

<br><br><br><br>

**6. [3 pts]** TDD (writing tests first) can produce better code, yet many developers
resist it. Why do you think that is?

<br><br><br>

**7. [2 pts]** The team is debating adopting **TypeScript** across the project. Fill in
the table.

| | |
|---|---|
| **Positive Outcomes** | |
| **Negative Outcomes** | |

<br>

**8. [8 pts]** For each **code smell**, name the **design pattern** that fixes it and give
a one-line reason. The first row is a model answer.

| Code smell | Pattern that helps | One-line reason |
|---|---|---|
| *Children buttons each get their own listener, and newly-added ones don't work* | *Event Delegation* | *One listener on the parent catches bubbled events, including from new children.* |
| `isLoading` and `isSuccess` are both `true` at once | | |
| A giant `switch` for every sort order inside the list component | | |
| The component hardcodes `localStorage` everywhere | | |
| A fetch that returns HTTP 500 is treated as success; the DOM is updated after the component is removed | | |

<br>

**9. [3 pts]** Fill in the blanks.

Patterns can create a shared ___________________ between professionals, but they can also
become exclusionary ___________________ . The in-class notes warn against
"JARGON___________________ ": language that chokes out understanding.

<br>

**10. [3 pts] Premature Optimization / Abstraction / Automation:**

Definition: ____________________________________________
<br>
Mitigation: ____________________________________________
<br>
Possible Cause(s): _____________________________________

<br>

**11. [12 pts] Security & Testing.**

The **Golden Rule of Web Security**: you cannot trust ___________________ , so trust no
data ___________________ or resulting outputs.

A Web Application Firewall can use a [ negative / blacklist ] [ positive / whitelist ]
[ behavioral / ML ] model. Which gives the **strongest** security but is **hardest** to
fully cover? ___________________ . Name one **tradeoff of the behavioral model**:

________________________________________________________________
<br><br>

For a single HTML form field (say, an email input), list **5 distinct test cases**:

1. ____________  2. ____________  3. ____________  4. ____________  5. ____________

<br>

Which STRIDE letter describes "a normal user gains admin abilities they should not
have"? ___________________

<br>

---

## Part B — Specifics: Web, JS, Git, Process [30 pts]

**12.** Short technical answers.

a) **[2]** Name two HTML **style-consistency** rules the guide recommends (e.g. casing,
quoting).

________________________________________________

b) **[3]** Name three HTML form attributes that improve validation or UX, and say what
each does (e.g. `required`, `maxlength`, `pattern`, `autocomplete`, `readonly`).

________________________________________________

c) **[2]** Why is link text like "click here" bad? What's better?

________________________________________________

d) **[2]** Finish the rule: "Format for development, ___________ for deployment." Name
one way to find **unused CSS**.

________________________________________________

e) **[3]** Name three checks you can run to catch problems **before** code enters the
repo (developer-local quality control).

________________________________________________

f) **[2]** A common branch strategy is `main` / `stage` / `dev` / feature branches. Why
should you **prune dead branches**?

________________________________________________

g) **[3]** What's the difference between a **unit test** and an **end-to-end (E2E)
test**? Give a one-line example of each.

________________________________________________

h) **[2]** Agile says to limit **WIP** — spell it out, and say why a giant backlog is a
problem.

________________________________________________

i) **[3]** Name the tool/site you'd use to **validate your HTML**, and say why
**semantic** valid HTML helps accessibility.

________________________________________________

j) **[2]** What does `// @ts-check` let you try, and give one caution about adopting full
TypeScript.

________________________________________________

k) **[3]** Write the git commands to (1) create and switch to a branch `feature-login`,
and (2) merge it into `main`.

________________________________________________

l) **[3]** Name two JavaScript "quirks/core ideas" the guide says to learn, and why you
should understand the **web platform** before reaching for a library.

________________________________________________

---

## Part C — Diagram, Table, AI, Scenario

**13. [9 pts]** A **state machine** transition table is shown below for reference.

```
 idle    --fetch----> loading
 loading --resolve--> success
 loading --reject---> error
 error   --retry----> loading
 success --reset----> idle
```

a) **[3]** What is *good* about modeling component state this way? (What does it prevent?)

<br><br>

b) **[3]** What code smell does this replace, and what's the **tradeoff** of using a
state machine?

<br><br>

c) **[3]** Is an explicit state machine worth it for *every* component? **Y / N** —
explain, otherwise write N/A.

<br><br>

**14. [10 pts]** Briefly explain each **architecture principle / idea**. Don't just name
it — give the **value** it provides *and* one limit or caution.

| Principle | What it means | Value + one caution |
|---|---|---|
| Form Follows Function | | |
| KISS | | |
| Single Responsibility Principle (SRP) | | |
| "It depends" / appropriate vs correct | | |
| Inversion of Control (IoC) | | |

<br>

**15. [10 pts] AI-driven SWE.**

a) **[3]** The in-class notes warn that leaning on generated code or jargon you don't
understand creates "understanding debt" / "career debt," and that **critical reasoning
is the main thing humans still have over code generators**. Explain what that means.

<br><br>

b) **[3]** AI may pull in a library or pattern that becomes **"your responsibility once
included."** How do **proportional testing / TDD + reviewing the generated code** reduce
this risk?

<br><br>

c) **[4]** Markdown/natural language now drives AI codegen (READMEs, prompts, AI output).
Using **trade-off thinking**, give one **strength** and one **problematic** aspect of
relying on natural-language specs to drive code generation.

<br><br>

**16. [10 pts] Fashion vs Foundation:** You join *FreshStack Inc.* The founder, **Trendy
McFashion**, insists every service be rewritten as **microservices** in the newest
framework "because that's what everyone uses," and wants simple app config stored in a
hand-rolled **binary** format "because JSON is old." These choices will be **hard to
undo**.

a) **[6]** Using **trade-off thinking** and the words **appropriate vs correct**, explain
what's wrong with choosing tech by **fashion**, what you'd ask *first* (name the warning
about "solution-first thinking"), and the danger of **premature microservice
decomposition**.

<br><br><br>

b) **[4]** This choice gets baked into the architecture (hard to reverse). What
**artifact** should you create to record the decision and why? Give one "**foundation
over fashion**" point you'd document.

<br><br><br>

---
---

# ANSWER KEY

> Model answers — key points a grader looks for. Wording can differ.

**1.** **Technical debt** = short-term decisions (rushing a design, skipping tests/
cleanup) that buy speed now but **add future cost** that must be "paid back" later.
**Technical investment** = work done now (research, refactoring, documenting
architecture, cleaning code) that **pays off later**. Good engineering acknowledges the
**risk of debt and the wisdom of investment** — and notes a "perfect" decision now might
not be perfect later.

**2.** "Debt" makes an invisible cost **visible and accountable** — it frames shortcuts
as something accruing interest, so teams can decide *deliberately* to take it on and plan
repayment. Yes, track it: untracked debt silently compounds into a "big ball of mud,"
and teams rewarded only for features tend to let it pile up.

**3.** Any three: **S**poofing identity, **T**ampering (with input), **R**epudiation
(no audit trail), **I**nformation disclosure, **D**enial of service, **E**scalation of
privilege.

**4.** *Positive:* drop-in analytics, no build step, easy. *Negative / ilities:*
**Security** (third-party JS over plain `http://` can be tampered in transit and runs
with full page privileges), **Reliability** (if the tracker host is down/slow the page
can break), **Performance** (render-blocking external script), **Maintainability**
(code changes outside your repo). *Better choice:* use **HTTPS**, load it `async`/
`defer` (or at end of body), add **Subresource Integrity**, vet the dependency ("once
included it's your responsibility"), and consider self-hosting; distrust third-party JS
in proportion to risk.

**5.** *Pros:* clicks are cheap to measure, give a usage signal, enable A/B comparison.
*Cons:* **metrics hide intention** — more clicks may mean the UI is **confusing or
manipulative**, not loved. Numeric simplifications mislead; chasing the number can
degrade real UX (dark patterns). Analytics need **interpretation + user-centered
judgment**, and KPIs slapped on after the fact often reveal weak up-front UCD. Also mind
the **testing effect** (people act differently when watched).

**6.** TDD is **slow to start**, has a real **learning curve/skill cost**, and feels
unproductive because you "write code that doesn't ship" first. Coding feels more
productive than testing; teams rewarded for shipping skip it — until untested/AI-
generated code creates failures or debt. (It may matter *more* with LLM codegen, which
needs strong verification.)

**7.** *Positive:* static types catch a class of bugs early, improve maintainability/
tooling/refactoring, document intent. *Negative:* **long-term cost/overhead**, learning
curve, build complexity, and it **does not replace runtime checks** for user-supplied
data. (Guide tip: try `// @ts-check` first before committing to full TS.)

**8.** Sample answers:

| Code smell | Pattern | Reason |
|---|---|---|
| `isLoading` & `isSuccess` both true | **State Machine** | One legal state at a time; impossible combinations are prevented. |
| Giant `switch` for every sort order | **Strategy** | Interchangeable functions share one interface; add behavior without editing the component. |
| Hardcoded `localStorage` everywhere | **Dependency Injection** | Depend on a storage *interface* passed in; swap/test the backend freely. |
| HTTP 500 treated as success; DOM updated after removal | **Robustness** | Check `res.ok` (fetch only rejects on network failure), use try/catch, timeouts, `AbortController`, and guard disconnected components. |

**9.** shared **language** … exclusionary **jargon** … "JARGON**monoxide**."

**10.** *Definition:* solving **future or imaginary problems early** — optimizing,
abstracting, or automating before there's a real, understood need. *Mitigation:* ask
**what is actually needed now** (YAGNI/KISS); "feel the hate before you automate"; build
from the core; grow complexity incrementally. *Causes:* wanting to look professional,
solution-first thinking, the "Lambo problem" (buying capability you don't need),
over-generalizing for edge cases that may never occur.

**11.** Golden Rule: you cannot trust **users (or their intentions)**, so trust no data
**inputs** or outputs. WAF: the **positive / whitelist** model is strongest but hardest
to cover (must enumerate all legitimate behavior). *Behavioral-model tradeoff:* training
data may **miss real usage**, creating **false positives** (and it can be fooled if the
"normal" baseline is wrong). *5 form tests:* valid input, empty input, wrong type/format,
boundary length, malicious/unexpected input. STRIDE letter = **E**, Escalation of
privilege.

**12.**
- a) Any two: lowercase elements/attributes, double-quoted attribute values, consistent
  empty-element style.
- b) Any three with purpose: `required` (must be filled), `maxlength` (caps length),
  `pattern` (regex format), `autocomplete` (browser fill), `readonly`/`disabled` (state),
  `type="email"` (right input type).
- c) "click here" is meaningless out of context (bad for accessibility/scanning); use
  **meaningful link text** describing the destination, and keep URLs readable/stable.
- d) "Format for development, **minify** for deployment." Find unused CSS with **devtools
  coverage**.
- e) Any three: read your own code, run local unit tests, IDE **linter**, **formatter**/
  style check, **EditorConfig**, **pre-commit hooks**.
- f) Dead branches pile up as clutter/cruft, cause confusion, and violate "neatness
  counts"; prune them (and clean up before the end).
- g) **Unit test** = one function/class/component in isolation (e.g. `add(2,3)===5`).
  **E2E test** = a full user workflow through the real interface (e.g. log in → add item
  → check out). Unit = many/fast/cheap; E2E = few/slow/broad.
- h) **Work In Progress.** Limiting it keeps focus and flow; a huge backlog **demotivates
  the team and users** and signals too much unfinished work.
- i) **https://validator.w3.org** (W3C validator; optionally an HTML-validator GitHub
  Action). Semantic HTML is **accessible by default** — screen readers/keyboards get
  correct structure/roles for free.
- j) `// @ts-check` lets you get TypeScript-style checking in plain JS **without
  committing to full TS**; caution: full TypeScript has **long-term cost** and doesn't
  replace runtime validation of user data.
- k) `git checkout -b feature-login` (or `git switch -c feature-login`); then on main,
  `git merge feature-login`.
- l) Any two: types, **closures**, OOP, **async**. Understand what the **platform gives
  you free** (e.g. `<dialog>`, `<details>`, native form validation) before re-building it
  with a library — and remember any library you adopt becomes your responsibility.

**13.**
- a) Only **one valid state at a time** and **legal transitions are explicit**, so
  **impossible/contradictory states become hard to create** and async flows are easier to
  reason about.
- b) Replaces **conflicting boolean flags** (`isLoading`/`hasError`/`isSuccess`). Tradeoff:
  **more upfront structure/boilerplate** (a transition table) for simple cases.
- c) **N.** Proportional/**appropriate**, not "correct for all" — a trivial component
  doesn't need a formal machine; reserve it for genuinely multi-state async flows.

**14.** Sample answers:

| Principle | Meaning | Value + caution |
|---|---|---|
| Form Follows Function | Design should follow what the system must **do**. | Keeps design grounded in purpose; caution: don't over-stylize before function is clear. |
| KISS | Keep it super simple; less code, fewer bugs. | Easier to read/maintain; caution: don't compress so far it becomes brittle/unreadable. |
| SRP | Each part has one focused responsibility. | High cohesion, limited blast radius of change; caution: over-splitting raises coupling/overhead. |
| "It depends" / appropriate vs correct | Real decisions depend on context, constraints, users, tradeoffs. | Fights one-true-way dogma; caution: not an excuse to avoid making a defensible decision. |
| Inversion of Control (IoC) | A framework/higher-level code calls *your* code; you write logic, it controls when it runs. | Enables flexible, pluggable design (DI); caution: can become **loss of control** if you don't understand the framework's behavior. |

**15.**
- a) Sounding technical ≠ understanding. Relying on generated code, vibes, or jargon you
  can't explain means you can't judge **tradeoffs, requirements, or hidden compromises** —
  so you accrue **understanding/career debt**. Critical reasoning about the *essence* of
  the system is the durable human skill; if the **core model** is wrong, you get
  "Death-Star-level" architectural flaws.
- b) A **dependency becomes part of your system and your responsibility**, with its own
  dependencies and risks. **Tests/TDD** create a **verification boundary** so generated
  code is checked against expected behavior before you trust it; **reviewing** it surfaces
  pattern bias, hidden tradeoffs, and security issues. Wrap it behind an **abstraction**
  so it can be replaced.
- c) *Strength:* natural language/Markdown is **readable, fast, and human-friendly** —
  easy to write specs/prompts and for AI to follow; keeps user-centered intent close.
  *Problem:* it's **ambiguous** — a vague spec/prompt yields vague or wrong software
  (same failure as vague requirements), with **no strict contract** like a type system or
  schema; you still need precision, tests, and acceptance criteria.

**16.**
- a) Choosing by **fashion** is **solution-first thinking** — picking the tool before
  understanding the problem, which **hides requirements and bakes in bad tradeoffs**.
  "New ≠ better," and the right word is **appropriate**, not **correct**: it *depends* on
  requirements, scale, team, and context. Ask first: what's the actual problem, who are
  the users, what scale/constraints, what do we trade away? **Premature microservice
  decomposition** adds **network/communication overhead, orchestration complexity, and
  failure points**, and can even *increase* coupling — solving luxury/future-scale
  problems that aren't real yet (the "Lambo problem").
- b) Create an **ADR (Architectural Decision Record)** — capture the decision, context,
  alternatives, tradeoffs, and *why* it's appropriate, because **architecture decisions
  are long-lasting and hard to change**, and future devs need to know *why*. A
  **foundation-over-fashion** point to document: high-level tech layers shift fast while
  low-level foundations move slowly; pick a format/architecture for **appropriateness and
  durable characteristics** (human-readability, robust parsing, replaceability) rather
  than trendiness — and if market pressure forces a fashionable choice, **say so honestly
  in the ADR**.

---

*Coverage: Testing & Quality (STRIDE, golden rule of security, 5 form tests, unit vs
E2E, TDD resistance, coverage/engagement metrics) • Architecture (technical debt vs
investment, premature optimization/abstraction, Form Follows Function/KISS/SRP/"it
depends"/IoC, ADRs, microservice tradeoffs) • In-class Architecture (fashion vs
foundation, appropriate vs correct, solution-first, jargon/JARGONmonoxide, security
models) • Patterns (State Machine, Strategy, Dependency Injection, Robustness, Event
Delegation) • Building & Deving (TypeScript/@ts-check, HTML style, forms, links, minify,
unused CSS, pre-commit checks, branches, WIP, w3 validator, JS quirks, git branch/merge)
• Operating & Evolving (analytics-driven design danger, the testing effect).*
