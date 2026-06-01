# CSE 110 Midterm 2 (Practice — Variant 5: CODE-FOCUSED) — Powell Spring 2026

Name: ______________________________   Score: ______ / 120

PID: ______________________   Team #: ______

> Same shape as Variant 4: roughly **60% read/review/refactor code, 40% conceptual
> reasoning**. All-new snippets drawn from the **capstones** (Notifications Hub 03,
> Todo-MVC 06, Sortable-Storable 09, Stateful-Form 12) plus CRUD (13) and Routing (14).
> Includes the **feature-toggle / A-B-testing** material (the Fowler reading). Answer Key
> at the bottom.

---

## Part A — Reading & Reasoning About Patterns [34 pts]

**1. [6 pts] Notifications Hub (pub/sub + delegation combined).**

```js
class NotificationsHub extends HTMLElement {
  constructor() { super(); this.attachShadow({ mode:'open' }); this.stacks = new Map(); }
  connectedCallback() {
    this.shadowRoot.innerHTML = `<style>${styles}</style>${template()}`; // has a <slot>
    this.addEventListener('notify', this.handleNotify.bind(this));   // ONE listener
  }
  handleNotify(event) {
    const { source, level, message, timestamp } = event.detail ?? {};
    if (!source) return;                       // (★)
    if (!this.stacks.has(source)) this.stacks.set(source, []);
    const stack = this.stacks.get(source);
    stack.push({ level, message, timestamp });
    if (stack.length > MAX_PER_SOURCE) stack.shift();
    this.renderStacks();
  }
}
```

a) **[2]** The hub hosts many `<status-publisher>` children via a `<slot>` but attaches
**one** `notify` listener on itself. Which two patterns combine here, and how does the hub
receive a child's event without referencing the child?

<br>

b) **[2]** `handleNotify` reads only `event.detail` and **never** does
`this.querySelector('status-publisher')`. Why is that the *point* — what coupling does it
avoid, and what happens when you add a 5th publisher later?

<br>

c) **[2]** What does the guard at ★ (`if (!source) return`) defend against, and what is
`.bind(this)` doing in the `addEventListener` call?

<br>

**2. [6 pts] The publisher side of the contract.**

```js
sendNotification() {
  const level = this.shadowRoot.querySelector('select[name="level"]').value;
  const raw   = this.shadowRoot.querySelector('input[name="message"]').value.trim();
  const message = raw || DEFAULT_MESSAGES[level];
  this.dispatchEvent(new CustomEvent('notify', {
    bubbles: true, composed: true,
    detail: { source: this.source, level, message, timestamp: Date.now() },
  }));
}
```

a) **[3]** `bubbles: true` and `composed: true` are both set. In one line each, what does
each flag do, and why does this component need **both**?

<br>

b) **[3]** `const message = raw || DEFAULT_MESSAGES[level];` — what is this guarding, and
which "-ility" does it serve? Is `||` appropriate here (vs `??`)?

<br>

**3. [8 pts] Todo-MVC — model + controller.**

```js
export class TodoModel extends EventTarget {
  #todos = []; #nextId = 1;
  list() { return [...this.#todos]; }
  add(text) {
    const trimmed = String(text ?? '').trim();
    if (!trimmed) return;                                  // (◆)
    this.#todos.push({ id: this.#nextId++, text: trimmed, done: false });
    this.#emit();
  }
  #emit() { this.dispatchEvent(new CustomEvent('todos-changed', { detail:{ todos:this.list() }})); }
}
// controller.js
export function connect(model, input, list) {
  if (input._connected || list._connected) return;        // (●)
  input._connected = list._connected = true;
  const handleIntent = (e) => {
    const { action, text, id } = e.detail;
    if (action === 'add') model.add(text);
    else if (action === 'toggle') model.toggle(id);
    else if (action === 'remove') model.remove(id);
  };
  input.addEventListener('intent', handleIntent);
  list.addEventListener('intent', handleIntent);
  model.addEventListener('todos-changed', (e) => { list.todos = e.detail.todos; });
}
```

a) **[2]** `TodoModel` extends `EventTarget` and uses `#todos`. Name **two** benefits
(one from `EventTarget`, one from the `#` private field).

<br>

b) **[3]** `list()` and the emit `detail` both return `[...this.#todos]` (a copy). Why
hand out a copy instead of the internal array? Which "-ility"/principle is this?

<br>

c) **[3]** What does the guard at ● prevent, and why can **one** `handleIntent` serve both
`<todo-input>` and `<todo-list>`?

<br>

**4. [6 pts] Decomposition — pick between.** Lesson 04 splits each component into
`component.js`, `component-template.js`, `component-styles.js`, `component-i18n.js`.

> **Option A:** one 400-line `article-viewer.js` with logic, an HTML string, a `<style>`
> block, and all UI text inline.
> **Option B:** the four-file split above.

a) **[3]** Give **two** concrete advantages of Option B and **one** real cost / when
Option A is fine.

<br>

b) **[3]** Which architectural ideas does Option B express (name at least two of:
**Separation of Concerns**, **SRP**, **cohesion**, **DRY**, **modularity**)? Explain one.

<br>

**5. [8 pts] Routing — read + spot the intentional limitation.**

```js
export class Router {
  constructor(outletEl, defaultPath = '/list') { this.outlet = outletEl; this.routes = new Map(); }
  register(path, elementName, title=null) { this.routes.set(path, { elementName, title }); return this; }
  start() {
    if (this._started) return;                    // (✦)
    this._started = true;
    window.addEventListener('hashchange', () => this.render());
    this.render();
  }
  currentPath() {
    const raw = window.location.hash.replace(/^#/, '');
    return (raw === '' || raw === '/') ? this.defaultPath : raw;
  }
  render() {
    const route = this.routes.get(this.currentPath());
    if (route) this.outlet.replaceChildren(document.createElement(route.elementName));
    else { /* "No route registered" */ }
  }
}
```

a) **[3]** In one or two sentences: what is "the **source of truth**" for which view is
shown, and why do the browser **back/forward** buttons work for free?

<br>

b) **[3]** A teammate adds `<a href="#features">` to scroll to an in-page section. It
renders **"No route registered for /features."** Explain the conflict (who **owns** the
hash now), and give one fix.

<br>

c) **[2]** What does the guard at ✦ (`if (this._started) return`) prevent if `start()` is
called twice?

<br>

---

## Part B — Robustness, State, Async [26 pts]

**6. [10 pts] Stateful-form capstone.** States: `idle → submitting → success | error`.

```js
// in connectedCallback — built ONCE:
this.shadowRoot.innerHTML = `
  <style>${styles}</style>
  <div class="form-container"></div>
  <div class="aria-live" role="status" aria-live="polite"></div>`;   // (A)

// submit handler:
this._lastSubmitted = this._message;
if (this._transition('submit')) this._submit();                       // (B)

async _submit() {                                                     // (C)
  try {
    await this._simulateNetwork();
    if (this._state !== 'submitting') return;                         // (D)
    this._transition('resolve');
  } catch (err) {
    if (this._state !== 'submitting') return;
    this._error = err.message;
    this._transition('reject');
  }
}
render() {
  this.shadowRoot.querySelector('.form-container').innerHTML = template(/* ... */); // (E)
  this.shadowRoot.querySelector('.aria-live').textContent = this._ariaMessage();
}
```

a) **[3]** At (A) the `.aria-live` region is built **once** and only its `textContent` is
updated, while `.form-container` (E) is fully re-rendered. Why split them — what breaks if
you recreate the live region on every render? Which "-ility"?

<br>

b) **[3]** At (B)/(C): the click handler is synchronous and calls `this._submit()`
**without `await`**, and `_submit` owns its **own** `try/catch`. What async bug does this
prevent? (Name the global event the robustness lesson uses to catch it.)

<br>

c) **[2]** Why gate the async call behind `if (this._transition('submit'))` instead of
just always calling `_submit()`?

<br>

d) **[2]** What is the defensive guard at (D) (`if (this._state !== 'submitting') return`)
protecting, given the comment says the current table can't even produce that case?

<br>

**7. [8 pts] CRUD — the backend interface.**

```js
export class MemoryBackend {
  #tasks = []; #nextId = 1;
  async list()         { await this.#delay(); return [...this.#tasks]; }
  async create(task)   { await this.#delay(); const t = {...task, id:this.#nextId++}; this.#tasks.push(t); return t; }
  async update(id, p)  { await this.#delay(); const t = this.#tasks.find(t=>t.id===id);
                         if (!t) throw new Error(`Task ${id} not found`); Object.assign(t, p); return t; }
  async remove(id)     { await this.#delay(); this.#tasks = this.#tasks.filter(t=>t.id!==id); }
}
// TasksService is a thin facade: list/create/update/remove just call the backend.
```

a) **[3]** The in-memory store needs no real `await`, yet **every** method is `async` /
returns a Promise. Why? What future change does this make painless?

<br>

b) **[3]** `update` **throws** when the id isn't found instead of silently returning.
Trace what the optimistic-update component does with that throw (happy path uses the
return value; failure path uses the throw).

<br>

c) **[2]** Why have a `TasksService` **facade** at all if it just forwards to the backend?
Name the pattern and the win.

<br>

**8. [8 pts] Strategy + DI together (Sortable-Storable capstone).**

```html
<sortable-storable-list strategy="byNameAsc" storage="localstorage"></sortable-storable-list>
```
The component looks up a comparator via `strategies[this.strategy]` (Strategy) and reads/
writes through an injected `storage` object exposing `save()` / `load()` (DI).

a) **[4]** The capstone's whole point: changing the **sort order** must not require
touching **storage** code, and vice-versa. Name the principle(s) this expresses
(orthogonality / SRP / separation of concerns) and explain why the two choices are
**independent axes**.

<br>

b) **[4]** Suppose an LLM "simplifies" by hardcoding `byNameAsc` and `localStorage`
directly inside `render()`. Name **two** things you lose (think: testing, swapping,
`if/else` growth) and the two patterns you'd reintroduce.

<br>

---

## Part C — Smells, Trade-offs, Process, GenAI, Scenario [60 pts]

**9. [10 pts] Spot the smells (JS).** Review this AI-written snippet. Identify **at least
5 distinct** problems and a fix for each.

```js
let cache = {};                                   // module-level mutable
function load(id) {
  setInterval(() => poll(id), 1000);              // started, never cleared
  fetch('/api/items/' + id).then(r => r.json()).then(data => {
    cache[id] = data;
    document.querySelector('#out').innerHTML = data.html;
  });
  let n = parseInt(data.count);                   // 'data' out of scope + parseInt no radix
  if (n == null) return;
}
```

<br><br>

**10. [6 pts] Spot the smells (HTML/CSS).** Identify the problems, give fixes, and name the
**"-ility"** hurt by each.

```html
<form>
  <input type="text" placeholder="Email">
  <input type="text" placeholder="Password">
  <div onclick="submitForm()">Submit</div>
</form>
<style> div { color: red !important; font-size: 11px; } </style>
```

<br><br>

**11. [8 pts] Trade-offs & the web model.**

a) **[3]** The notifications hub calls `renderStacks()` — it re-renders **only**
`#stacks-grid`, leaving the slotted publishers untouched. The stateful form fully
re-renders `.form-container`. Compare **targeted update** vs **full re-render** — one
benefit and one cost of each.

<br>

b) **[3]** Map each REST action to an HTTP method, and say why "URLs are nouns, methods
are verbs": create ▢, read ▢, update ▢, delete ▢.

<br>

c) **[2]** The CRUD/robustness demos hit a real endpoint and "flake" when it's slow/down.
Name the **"-ility"** this exposes and the **bold-named effect** where local dev hides
real network behavior.

<br>

**12. [8 pts] Agile ↔ tooling (incl. the Fowler reading).**

a) **[3]** Define a **feature flag / feature toggle**. How does it let a team merge and
deploy unfinished work safely (tie to trunk-based / small-batch iteration)?

<br>

b) **[3]** What is **A/B testing**, and how do feature flags enable it? Give one risk of
deciding by A/B metrics alone (recall: "more clicks ≠ love").

<br>

c) **[2]** Two teammates edit the same lines on different branches and hit a **merge
conflict** on PR. Whose job is it to resolve it, and why are **small, frequent** merges
better than one big one?

<br>

**13. [6 pts] TDD & BDD on the submit form.**

a) **[3]** Write a **BDD-style** scenario (Given / When / Then) for the form's
**failure-then-retry** path. (Prose is fine — you're describing behavior, not code.)

<br>

b) **[3]** Place these on the **testing pyramid** (which are many/few, fast/slow): a unit
test of `next(state, event)`; an E2E test that submits the form in a real browser; an
integration test of model↔controller wiring. Why that shape?

<br>

**14. [8 pts] GenAI reasoning.**

a) **[4]** An LLM rewrote the submit form and **deleted the persistent `aria-live`
region**, recreating it on each render "to simplify." The demo still looks fine to a
sighted developer. Explain how this is **"preservation of complexity"** — the difficulty
didn't vanish, it **moved** (to whom?) — and what the reviewer needed to know to catch it.

<br>

b) **[4]** Using **trade-off thinking**: AI shifts engineering effort from **writing**
code to **reviewing/verifying** it. Give one task that genuinely gets **easier** and one
that gets **harder or more important**, and connect it to **tech debt** and "more code =
more bugs."

<br>

**15. [8 pts] Pick-between capstone.** A PR manages a fetch UI two ways:

> **Design P (flags):** `let isLoading=false, isError=false, isSuccess=false;` toggled
> across several handlers.
> **Design Q (machine):** a `transitions` table + `next(state, event)`; the component
> holds a single `this._state`.

a) **[4]** Pick the better design for anything beyond trivial UI and justify it using the
"**impossible states**" idea (what can Design P represent that Design Q cannot?).

<br>

b) **[4]** Before approving the merge, name **two** things you'd require (tests for invalid
transitions, an `aria-live` status, `res.ok`/error handling, listener/timer cleanup in
`disconnectedCallback`, an ADR) and why each matters.

<br>

**16. [6 pts] Your team / project.** (Easy points if you've been involved.)

a) **[2]** Name a place your project does (or should do) **event-based** communication
between components, and why direct calls would be worse.

b) **[2]** Name one **robustness or accessibility** decision your team made (or should), and
the "-ility" behind it.

c) **[2]** Name one **documentation artifact** your team used (ADR, JSDoc, README) and what
future-you gains from it.

<br>

---
---

# ANSWER KEY

> Model answers — key points a grader looks for. Wording can differ.

**1.**
- a) **Pub/Sub** (children fire `notify` events) + **Event Delegation** (one parent
  listener). The publishers' events **bubble up through the `<slot>`** (and escape their
  shadow DOM via `composed`) to the hub, which listens on itself — so the hub never holds
  a reference to any child.
- b) Reading only `event.detail` keeps the hub depending on the **event contract**, not
  each child's internals/markup — **loose coupling**. Adding a 5th publisher requires
  **zero changes to the hub**: it just emits the same `notify` event and the existing
  delegated listener routes it.
- c) `if (!source) return` drops **malformed events** (missing/garbage `detail`) so they
  don't create a bogus stack — a small **robustness** guard. `.bind(this)` fixes
  `this` to the hub instance inside `handleNotify` (and gives a **stable reference** you
  could later `removeEventListener`).

**2.**
- a) `bubbles: true` = the event travels **up the DOM tree** to ancestor listeners.
  `composed: true` = the event can **cross the shadow-DOM boundary** out of this
  component. It needs both because the publisher is **inside a shadow root** *and* the
  listener (the hub) is an **ancestor** — miss either and the hub never hears it.
- b) It supplies a **sensible default message** when the user leaves the input empty —
  serves **usability** (no blank/confusing notification). `||` is appropriate here because
  an **empty string** (after `.trim()`) is falsy and *should* fall through to the default;
  `??` would only catch `null`/`undefined` and would let `""` pass.

**3.**
- a) `EventTarget` gives `addEventListener`/`dispatchEvent` for free, so the **Model can
  emit change events** the Controller subscribes to like any DOM node. `#todos` is **truly
  private** (encapsulation) — outside code can't reach in and mutate state, only the
  Model's methods can.
- b) Handing out a **copy** preserves **encapsulation / data hiding**: callers can read the
  list but can't mutate the Model's internal array behind its back (which would skip the
  change event and corrupt state). It keeps the Model the single source of truth.
- c) ● prevents **double-wiring** (binding the same listeners twice → events handled
  twice). **One** `handleIntent` works because both views emit the **same `intent`
  contract** (`{action, text, id}`); the handler branches on `action`, so the source view
  doesn't matter.

**4.**
- a) Advantages (any two): easier to **read/navigate**, change one concern (styles, text,
  logic) **without touching** the others, easier to **test** logic in isolation, reusable
  pieces, parallel work. Cost / Option A fine: **more files to navigate**; for a tiny,
  one-off component, splitting is **over-decomposition** that adds ceremony for little gain.
- b) **Separation of Concerns** (logic vs markup vs styling vs i18n), **SRP** (each file
  one responsibility), **high cohesion** (related stuff together), **modularity** (swap one
  module without the rest). E.g. translations living in `-i18n.js` means adding a language
  doesn't touch logic.

**5.**
- a) The **URL hash** (`window.location.hash`) is the source of truth — `render()` reads
  it to pick the view. Back/forward work for free because those buttons just **change the
  hash**, firing `hashchange`, which the router already listens to.
- b) The router now **owns the hash** as an app-wide resource, so an in-page anchor
  (`#features`) is interpreted as a **route path** `/features`, which isn't registered →
  "No route." Fix: use **scroll-to-id JS** / `scrollIntoView`, or put in-page targets
  behind a query param on a real route — don't use bare `#anchor` links.
- c) ✦ prevents a **second `hashchange` listener** from being bound, which would make the
  router `render()` **twice per navigation** (double work / duplicated side effects).

**6.**
- a) Screen readers (NVDA/JAWS/VoiceOver) only reliably **announce changes** to a live
  region that **persists**; destroying and recreating it on every render makes
  announcements **inconsistent or missed**. So the live region stays put and only its
  `textContent` mutates, while the visual form can fully re-render. "-ility":
  **accessibility** (also usability).
- b) It prevents an **unhandled promise rejection**: if `_submit()` were `await`ed in a
  sync handler (or left floating with no `try/catch`), a thrown error would escape with no
  catcher. Because `_submit` wraps its own `try/catch`, every failure is funneled into the
  `error` state. The global net for ones that *do* escape is
  **`window.addEventListener('unhandledrejection', …)`**.
- c) `_transition('submit')` returns **false for an illegal transition** (e.g. a stray
  submit while already `submitting`); gating on it means you **don't schedule a network
  call** for a transition the state machine rejected.
- d) It guards against the **state moving off `submitting`** (e.g. user cancelled / a
  future transition) **while the await was pending** — so a late-resolving submit doesn't
  stomp a now-different state. Defensive against the table **growing** later, even if
  today's table can't produce it.

**7.**
- a) Returning Promises everywhere makes the **interface identical for sync (memory) and
  async (REST) backends**, so the calling component `await`s the same way regardless. The
  painless future change: **swap `MemoryBackend` for a `RestBackend`** without touching the
  component or service.
- b) **Happy path:** `update` resolves with the (possibly server-modified) task; the
  component reconciles `this._tasks` to that returned object. **Failure path:** the throw
  is caught in the component's `catch`, which **reverts** the optimistic change to
  `original` and shows an error toast.
- c) **Facade** (a CRUD service layer). Win: the component depends on a **stable
  `list/create/update/remove` interface**; backend details (memory, REST, validation,
  shaping `create` payloads) are **hidden** and can change without touching the UI.

**8.**
- a) **Separation of Concerns / SRP / orthogonality**: sorting and storage are unrelated
  responsibilities, so they're **independent axes** — `strategy` decides *order*,
  `storage` decides *where data lives*. Changing one (Strategy lookup) can't affect the
  other (injected `save/load`) because they share no code path.
- b) You lose **testability** (can't unit-test sorting without a browser/localStorage) and
  **swappability** (can't change sort or storage without editing `render()`), and you
  invite a **growing `if/else`** for new sort modes. Reintroduce **Strategy** (comparator
  lookup) and **Dependency Injection** (pass a storage interface).

**9.** Any five+ of:
1. `let cache = {}` module-level mutable shared state → encapsulate or scope it (and it
   grows unbounded → leak).
2. `setInterval(..., 1000)` is **never cleared** → keeps polling after the element is gone
   (leak); store the id and `clearInterval` in `disconnectedCallback`.
3. `fetch` has **no `.catch` / no `res.ok`** check → a 500 or network error is unhandled;
   use `try/catch` + check `res.ok`.
4. `el.innerHTML = data.html` with server data → **XSS** risk; sanitize or use safe DOM
   APIs.
5. **`data` is out of scope** outside the `.then` (and runs before the fetch resolves) →
   logic bug; move it inside the `.then`/use `await`.
6. `parseInt(data.count)` with **no radix** → pass `10` (`parseInt(x, 10)`).
7. **`== null`** loose check (acceptable to some, but the snippet mixes styles) → prefer
   `=== null`/`=== undefined` or `Number.isNaN`.
8. **String-concatenated URL** with raw `id` → `encodeURIComponent`.
9. No `async/await`; nested `.then` is harder to read (favor reading over writing).

**10.**
- `<input type="text" placeholder="Email">` (and Password) → use **`type="email"` /
  `type="password"`**, add a real **`<label>`** (placeholder ≠ label) — **accessibility/
  usability**; password as `text` also leaks the field on screen (**security/privacy**).
- `<div onclick="submitForm()">Submit</div>` → use **`<button type="submit">`** —
  not keyboard-focusable/operable as a div (**accessibility**); inline `onclick` violates
  separation of concerns (**maintainability**, CSP/**security**).
- `color: red !important` → avoid `!important` (specificity wars) — **maintainability**.
- `font-size: 11px` → use relative units (`rem`) so it scales with user settings —
  **accessibility**. (Also the bare `div {}` selector is over-broad.)

**11.**
- a) **Targeted update** (`renderStacks`): fast, preserves focus and untouched regions —
  but more code/bookkeeping and risk of the DOM drifting from state. **Full re-render**:
  dead simple and always consistent with state — but **destroys DOM**, **loses keyboard
  focus**, and is less efficient.
- b) create = **POST**, read = **GET**, update = **PUT/PATCH**, delete = **DELETE**. URLs
  name the **resource** (a thing/noun, `/tasks/1`); the method names the **action**
  (verb) performed on it.
- c) **Reliability/availability** (depending on a flaky external endpoint is fragile). The
  effect is the **"Localhost Effect"** — local dev hides real network latency/failure, so
  the app looks more reliable than it is in production.

**12.**
- a) A **feature toggle** wraps new functionality in a condition so it can be merged/
  deployed **off** (or shown to a subset of users). This enables **trunk-based, small-batch
  iteration**: code integrates continuously without long-lived branches, and risky work
  ships dark until it's ready — a failed experiment is a flag flip, not a revert.
- b) **A/B testing** shows variant A to some users and B to others and compares outcomes;
  a flag routes the cohorts. Risk: metrics **hide intention** — more clicks/engagement may
  mean **confusion or dark patterns**, not value; decide with user-centered judgment, not
  the number alone.
- c) Whoever's PR is **merging in** resolves the conflict (you rebase/merge `main` into
  your branch and fix it). **Small, frequent** merges keep conflicts tiny and local; one
  **big** merge collides with everything at once and is far harder/riskier to reconcile.

**13.**
- a) e.g. **Given** the form is `idle` with a typed message, **When** the user submits and
  the (simulated) network **rejects**, **Then** the form enters the `error` state and shows
  a contextual error with **Retry**/**Cancel**; **When** the user clicks **Retry** and the
  next attempt **resolves**, **Then** the form enters `success` and announces it via the
  live region.
- b) **Many** fast **unit** tests at the base (`next(state,event)` — pure, instant);
  **fewer** **integration** tests in the middle (model↔controller wiring); **few** slow,
  broad **E2E** tests on top (real-browser submit). Shape: small tests are cheap/fast/run
  often; E2E is slow/fragile so you keep it sparse — proportional to risk.

**14.**
- a) The accessibility requirement didn't disappear — the **complexity moved onto the
  users** (screen-reader users now miss status announcements) and onto **future
  maintainers** who must rediscover why a persistent live region was needed. That deferred,
  hidden cost is **tech debt**. The reviewer had to know **how `aria-live` actually behaves
  across screen readers** to see that "simpler" code silently broke a real requirement.
- b) **Easier:** generating boilerplate / a first draft / scaffolding. **Harder / more
  important:** **reviewing, verifying, and owning** it — judging trade-offs, catching
  silent regressions (a11y, `res.ok`, leaks), writing tests. AI emits lots of plausible
  code fast, and **more code = more bugs/surface area**, so the bottleneck shifts to
  **critical evaluation** and the risk shifts to **unnoticed tech debt**.

**15.**
- a) **Design Q (state machine).** Design P's separate booleans can combine into
  **impossible states** — `isLoading && isSuccess` both true, or all three false — that no
  legal flow should allow. A state machine holds **one** state at a time and only permits
  **declared transitions**, so contradictory states are **unrepresentable**.
- b) Any two with reasons: **tests for invalid transitions** (prove illegal events are
  no-ops); a persistent **`aria-live`** status (accessibility); **error handling/`res.ok`**
  on the real fetch; **timer/listener cleanup in `disconnectedCallback`** (no leaks/
  detached writes); an **ADR** recording why a state machine over flags (it shapes the
  component's architecture).

**16.** *(Personalize — sample shapes.)*
- a) e.g. "Child cards emit a `select` event the parent list listens for — direct calls
  would hardwire each card to the parent and break when cards are added dynamically."
- b) e.g. "We check `res.ok` and show a retry on failure (**reliability**)," or "semantic
  HTML + `alt` + a live region for status (**accessibility**)."
- c) e.g. "An **ADR** for choosing hash routing / a storage backend, so future teammates
  know *why*," or "**JSDoc** so docs generate from code and don't go stale."

---

*Source patterns: notifications hub (03 — pub/sub + delegation), components/decomposition
(04), todo-MVC (06), sortable-storable (09 — strategy + DI), stateful-form (12 — state
machine + robust async + aria-live), CRUD service/backend (13), routing (14). Concepts:
coupling/cohesion, SRP, separation of concerns, encapsulation/data hiding, DRY,
abstraction, DI, strategy, pub/sub, event delegation, MVC, web components, REST verbs,
client-server & the Localhost Effect, targeted vs full re-render, code smells (leaked
intervals, XSS, parseInt radix, placeholder-as-label, `!important`, div-as-button),
feature toggles & A/B testing, merge conflicts & trunk-based flow, testing pyramid,
TDD/BDD, accessibility (aria-live), ADR/JSDoc docs, and GenAI's effect on tech debt and
the preservation/relocation of complexity.*
