# CSE 110 Midterm 2 (Practice — Variant 4: CODE-FOCUSED) — Powell Spring 2026

Name: ______________________________   Score: ______ / 120

PID: ______________________   Team #: ______

> **This variant is different on purpose.** Variants 1–3 were conceptual (Midterm-1
> style). Per the exam guidance, Midterm 2 will make you **read, review, refactor,
> comment on, or pick between code** — as if you're reviewing a teammate's or an LLM's
> work. You will **not** write code from scratch, but you must *understand* it and be
> able to *rearrange* the constructs given.
>
> All snippets are adapted from the lecture pattern code (`sample-pattern-code` lessons
> 01–06, `Batch-2-of-patterns` lessons 07–14). Answer Key at the bottom.

---

## Part A — Reading & Reasoning About Patterns [34 pts]

**1. [6 pts] Pub/Sub.** A publisher component fires an event when its button is clicked:

```js
publishMessage() {
  const event = new CustomEvent('custom-message', {
    detail: { msg: this.messageInput.value },
    bubbles: true,
    composed: true,
  });
  this.dispatchEvent(event);          // dispatched on the host element
}
// Subscriber:
connectedCallback()    { document.addEventListener('custom-message', this.handleMessage); }
disconnectedCallback() { document.removeEventListener('custom-message', this.handleMessage); }
```

a) **[2]** The publisher uses a shadow DOM; the event-delegation `<tile-component>` from
lesson 02 renders into the **light DOM** (`this.innerHTML`) and sets only `bubbles: true`.
Why does the pub/sub component also need **`composed: true`**?

<br>

b) **[2]** What does the publisher know about the subscriber? Name the **architectural
benefit** this buys (use the words coupling/decoupling).

<br>

c) **[2]** Why remove the listener in `disconnectedCallback()`? What **code smell / bug**
appears if you forget?

<br>

**2. [6 pts] Event Delegation — pick between two versions.**

```js
// Version A
document.querySelectorAll('tile-component button')
  .forEach(btn => btn.addEventListener('click', onClick));

// Version B
document.getElementById('tiles').addEventListener('tile-clicked', (event) => {
  const tile = event.target.closest('tile-component');
  if (!tile) return;
  log.textContent = `Last clicked: ${event.detail.label}`;
});
```

a) **[3]** A new `<tile-component>` is added to `#tiles` **after** page load. Which
version handles its clicks **without re-binding**, and why?

<br>

b) **[3]** The lesson comment warns: *"We can break this code if we break the HTML
structure."* Version B reads `event.detail.label` instead of reaching into the tile's
internals. Why is reading `event.detail` **less coupled** than the parent querying the
child's DOM?

<br>

**3. [8 pts] MVC review.** Counter example, three files:

```js
// model.js
export class CounterModel extends EventTarget {
  #value = 0;
  get value() { return this.#value; }
  increment() { this.#value++; this.#emit(); }
  #emit() { this.dispatchEvent(new CustomEvent('value-changed', { detail:{ value:this.#value }})); }
}
// controller.js
view.addEventListener('intent', e => { if (e.detail.action === 'increment') model.increment(); });
model.addEventListener('value-changed', e => view.setAttribute('value', String(e.detail.value)));
// view (CounterView): on button click → dispatch CustomEvent('intent', { detail:{action} })
//                      on attribute 'value' change → re-render the number
```

a) **[2]** Name the three roles and state **which one knows about the other two**.

<br>

b) **[3]** The View dispatches an `intent` event rather than calling `model.increment()`
directly. Name **two "-ilities"** this improves and why.

<br>

c) **[3]** A teammate "simplifies" by importing the model into the View and calling
`model.increment()` inside the click handler. Which principle does this break
(Separation of Concerns / coupling), and give **one concrete downside** for testing or
reuse.

<br>

**4. [6 pts] Strategy — refactor the smell.** An LLM produced this:

```js
function sortItems(items, mode) {
  if (mode === 'byNameAsc')        return items.sort((a,b)=>a.name.localeCompare(b.name));
  else if (mode === 'byNameDesc')  return items.sort((a,b)=>b.name.localeCompare(a.name));
  else if (mode === 'byDateNewest')return items.sort((a,b)=>b.createdAt - a.createdAt);
  else if (mode === 'byDateOldest')return items.sort((a,b)=>a.createdAt - b.createdAt);
}
```

a) **[3]** Name the smell and the pattern that removes it. What is the shared **contract
/ signature** that makes the alternatives interchangeable?

<br>

b) **[3]** Rearrange the constructs above into a **strategies lookup object** and show how
the caller would pick one (you may reuse the comparators verbatim — this is rework, not
from scratch).

<br>

**5. [8 pts] Dependency Injection — read + spot the bug.**

```js
// storage.js
export class StorageFactory {
  static createStorage(type, options = {}) {
    switch (type.toLowerCase()) {
      case 'memory':       return new MemoryStorage();        // save/load/getName
      case 'localstorage': return new LocalStorageAdapter();  // save/load/getName
      default:             return new MemoryStorage();
    }
  }
}
// task-list.js (web component) — excerpt
render() {
  this.innerHTML = `... <button class="add-btn">Add</button> <ul>...</ul>`;
  this.setupListeners();                 // called at the END of every render()
}
setupListeners() {
  this.addEventListener('click', e => {  // attaches a NEW listener on the host
    if (e.target.classList.contains('add-btn')) this.addTask(/* ... */);
  });
}
```

a) **[2]** Does the component depend on a **concrete backend** or an **interface**? Name
the interface methods.

<br>

b) **[2]** `createStorage(type, options = {})` — `options` is currently unused. The
lesson comment ties it to the Prof's "trap doors / elastic signatures" idea. Why add it
**now**?

<br>

c) **[4]** **Spot the bug:** `render()` runs on every change and ends by calling
`setupListeners()`, which calls `this.addEventListener('click', …)`. What goes wrong over
time, and what's the fix (where should that listener live)?

<br>

---

## Part B — Robustness, State, Async [26 pts]

**6. [10 pts] Robustness — `fetchWithTimeout`.**

```js
async function fetchWithTimeout(url, ms) {
  const controller = new AbortController();
  const timer = setTimeout(() => controller.abort(new TimeoutError(ms)), ms);
  try {
    const res = await fetch(url, { signal: controller.signal });
    if (!res.ok) throw new Error(`HTTP ${res.status} ${res.statusText}`);
    return res;
  } finally {
    clearTimeout(timer);
  }
}
// caller, inside a component, after awaiting:
if (!this.isConnected) return;
this.showError(err.message);
```

a) **[3]** `fetch` already returns a promise — so why the explicit `if (!res.ok) throw`?
What real-world failure does fetch **NOT** reject for?

<br>

b) **[3]** Why `AbortController` instead of `Promise.race([fetch, timer])`? What does the
`race` approach leave running, and which "-ility" does that hurt?

<br>

c) **[2]** Why a custom `TimeoutError` class instead of `throw new Error('timeout')` +
checking `err.message`?

<br>

d) **[2]** What is the `if (!this.isConnected) return` guard protecting against?

<br>

**7. [8 pts] State Machine.**

```js
export const transitions = {
  idle:    { fetch: 'loading' },
  loading: { resolve: 'success', reject: 'error' },
  success: { reset: 'idle' },
  error:   { reset: 'idle', retry: 'loading' },
};
export function next(state, event) {
  const target = transitions[state]?.[event];
  if (!target) { console.warn(`Invalid: ${state} can't handle ${event}`); return state; }
  return target;
}
```

a) **[3]** A user somehow triggers `retry` while in `success`. Trace `next('success',
'retry')` — what is returned, and what does the UI do? Why is that the **desired**
behavior?

<br>

b) **[3]** How does this design prevent the "boolean soup" smell
(`isLoading && isSuccess` both true)? What invariant does the table guarantee?

<br>

c) **[2]** A lesson comment admits a `setTimeout` can still fire after the element is
removed, mutating a detached shadow root. Which **later lesson** addresses this, and
what's the one-line idea of the fix?

<br>

**8. [8 pts] CRUD — optimistic update review.**

```js
async _update(id, patch) {
  if (this._pendingIds.has(id)) return;          // (★)
  this._pendingIds.add(id);
  const original = this._tasks.find(t => t.id === id);
  this._tasks = this._tasks.map(t => t.id === id ? { ...t, ...patch } : t);  // optimistic
  this.render();
  try {
    const serverTask = await this._service.update(id, patch);               // (▲)
    this._tasks = this._tasks.map(t => t.id === id ? serverTask : t);
  } catch (err) {
    this._tasks = this._tasks.map(t => t.id === id ? original : t);          // revert
    this._showOpError('Update failed: ' + err.message);
  } finally {
    this._pendingIds.delete(id);
    this.render();
  }
}
```

a) **[3]** Describe what the user sees on the **happy path** vs. the **failure path**.

<br>

b) **[3]** Line ▲ reassigns `this._tasks` from `serverTask` *after* we already applied
the patch optimistically. Why bother? What might a **REST** backend return that the
in-memory one doesn't?

<br>

c) **[2]** What does the `_pendingIds` guard at ★ prevent, and which "-ility" does it
protect?

<br>

---

## Part C — Smells, Trade-offs, Process, GenAI, Scenario [60 pts]

**9. [10 pts] Spot the smells (JS).** An LLM submitted this. Identify **at least 5
distinct** problems and give a fix for each.

```js
function getUser(u) {
  var d = fetch('http://api.example/users/' + u).then(r => r.json());
  return d;
}
async function show(id) {
  let el = document.getElementById('out');
  let res = await fetch('http://api.example/users/' + id);
  let user = await res.json();
  if (user.role == 'admin') {
    el.innerHTML = user.name;
  }
  setTimeout(() => { el.style.top = 200 + 'px'; }, 3000);
}
```

<br><br>

**10. [6 pts] Spot the smells (HTML/CSS).** Identify the problems, give fixes, and name
the **"-ility"** hurt by each.

```html
<div class="nav">
  <div class="link" onclick="go('/home')">Home</div>
</div>
<img src="logo.png">
<p style="color:#aaa; background:#bbb">Welcome back!</p>
```

<br><br>

**11. [8 pts] Trade-offs & the web model.**

a) **[3]** The components above call `this.shadowRoot.innerHTML = ...` — a **full
re-render** on every state change. Give **one benefit** and **one cost** of full
re-render (the lesson comments name the cost). Which "-ility" suffers?

<br>

b) **[3]** The pub/sub demo ends with `<noscript>JavaScript is required…</noscript>`,
and a JS-only UI **fails with no JavaScript**. Define **graceful degradation** vs.
**progressive enhancement**, and say which mindset this `<noscript>` reflects.

<br>

c) **[2]** The hash router (lesson 14) makes one HTML page swap views via
`#/list` / `#/prefs`. Is that closer to an **SPA** or an **MPA**? Name one trade-off of
that choice.

<br>

**12. [8 pts] Agile ↔ tooling.**

a) **[3]** Why is **CI/CD** "such a big deal" *with Agile* specifically? Tie it to small,
safe iteration.

<br>

b) **[3]** Why do we do **pull requests**? Name two things review catches, and one reason
**large** PRs get weak reviews.

<br>

c) **[2]** List the typical **steps in a build pipeline**, in a sensible order (name at
least four).

<br>

**13. [6 pts] TDD & BDD.**

a) **[2]** State the three steps of **TDD** and what each produces.

<br>

b) **[2]** Why is TDD **not used as much** in practice?

<br>

c) **[2]** How might **TDD/BDD** become *more* useful **going forward** with LLM code
generation?

<br>

**14. [8 pts] GenAI reasoning.**

a) **[4]** An LLM was asked for the task-list feature and produced a version that
**hardcodes `localStorage`** directly inside the component (no injection). It runs fine
in the demo. Explain how this **relocates/preserves the difficulty** rather than removing
it, and what **tech debt** it creates. What did the human reviewer still need to
understand to catch it?

<br>

b) **[4]** Using **trade-off thinking**: GenAI shifts *which* SWE steps are hard. Argue
what gets **easier** and what gets **harder** (e.g., producing code vs. reviewing,
verifying, understanding, and owning it). Tie it to "more code = more bugs."

<br>

**15. [8 pts] Pick-between capstone.** Your teammate (with AI help) opens a PR for a
"notifications" feature. Two designs are proposed:

> **Design X:** Each child status component holds a **direct reference** to the parent
> hub and calls `hub.addNotification(...)` itself.
>
> **Design Y:** Each child **dispatches a `notify` CustomEvent** (`bubbles`/`composed`);
> the hub has **one delegated listener** and reads `event.detail`.

a) **[4]** Pick the better design and justify it using **coupling**, **adding a new
publisher later**, and **dynamically added children**.

<br>

b) **[4]** Before you approve the merge, name **two** things you'd require (think: tests,
ADR, error handling, `res.ok`, listener cleanup, docs) and say why each matters.

<br>

**16. [6 pts] Your team / project.** (Easy points if you've been involved.)

a) **[2]** Name one **pattern** from this list your team used (or could use) and where.

b) **[2]** Name one **"-ility"** that drove a real decision on your project, and the
decision it drove.

c) **[2]** Name one **code smell** you cleaned up (or a documentation form you used — ADR,
JSDoc, README) and why it helped.

<br>

---
---

# ANSWER KEY

> Model answers — the key points a grader looks for. Wording can differ.

**1.**
- a) `composed: true` lets a CustomEvent **cross the shadow-DOM boundary** and reach
  listeners outside the component (e.g. on `document`). The pub/sub component lives in a
  shadow tree, so without `composed` the event can't escape; the `<tile-component>` uses
  **light DOM** (`this.innerHTML`, no `attachShadow`), so `bubbles: true` alone reaches
  `document`.
- b) **Nothing** — the publisher never references the subscriber; it just announces "this
  happened." That's **loose coupling / decoupling**: either side can change or be
  reused independently, and multiple subscribers can listen to one event.
- c) Removing it prevents a **memory leak** (and duplicate handling): a `document`-level
  listener tied to a removed element keeps the element alive and keeps firing. Forgetting
  it is the "listener never cleaned up / leak" smell. (Note the handler is `bind`-ed once
  so add/remove use the **same reference**.)

**2.**
- a) **Version B.** The listener is on the parent `#tiles`, so any child's
  `tile-clicked` event **bubbles up** to it — including children added after load. Version
  A bound listeners to the specific buttons that existed at query time, so new tiles get
  nothing until you re-bind.
- b) Reading `event.detail` means the parent depends only on the **event contract** (the
  data the child chose to publish), not on the child's internal DOM/structure. If the
  parent instead did `tile.querySelector('h2').textContent`, any markup change inside the
  child breaks it — that's tight structural coupling. The trade-off: `closest()` still
  depends a bit on the tag structure, but `detail` keeps the data contract stable.

**3.**
- a) **Model** (data + rules, emits `value-changed`), **View** (UI, emits `intent`,
  reflects `value`), **Controller** (wires the two). The **Controller** is the only piece
  that knows about both Model and View; Model and View don't know each other.
- b) Any two: **Maintainability/Modularity** (View and Model change independently),
  **Testability** (Model is DOM-free → unit-testable; View can be tested without a real
  model), **Reusability** (same View with a different model/controller). Reason: the
  `intent`/event seam decouples *what the user wants* from *how state changes*.
- c) Breaks **Separation of Concerns** and creates **tight coupling** (View now depends on
  the Model's concrete API). Downside: you can no longer test the View without
  instantiating the real Model, can't reuse the View with a different model, and business
  logic leaks into a DOM event handler.

**4.**
- a) Smell: **giant `if/else` chain for interchangeable behavior** (and missing
  `default`). Pattern: **Strategy**. Contract: every comparator shares the signature
  **`(a, b) => number`**, so the caller can use any of them identically.
- b) Rework:
  ```js
  const strategies = {
    byNameAsc:   (a,b) => a.name.localeCompare(b.name),
    byNameDesc:  (a,b) => b.name.localeCompare(a.name),
    byDateNewest:(a,b) => b.createdAt - a.createdAt,
    byDateOldest:(a,b) => a.createdAt - b.createdAt,
  };
  function sortItems(items, mode) {
    const fn = strategies[mode] ?? strategies.byNameAsc;  // fallback
    return [...items].sort(fn);
  }
  ```
  Adding a new sort = adding one key, no edits to `sortItems`. (Bonus: copying the array
  avoids mutating the caller's input.)

**5.**
- a) An **interface/abstraction** — the component only needs an object with
  **`save()`, `load()`, `getName()`**. The concrete class (Memory vs LocalStorage) is
  chosen by the factory and injected, so it can be swapped without touching the component.
- b) Adding `options` now keeps the **signature elastic / future-proof** ("trap door"):
  later backends (a REST adapter, a key/namespace, credentials) can be configured
  **without breaking every existing caller**. Hardcoding a rigid signature now means a
  painful migration later.
- c) **Bug:** every `render()` attaches **another** `click` listener to the host, so they
  **accumulate** — after N renders one click fires N handlers (duplicate `addTask`,
  duplicate tasks, growing memory). **Fix:** attach the delegated listener **once** in the
  `constructor` (or `connectedCallback`), not inside `render()`/`setupListeners()` — the
  same "delegated handler in the constructor (runs once)" pattern the other lessons use.

**6.**
- a) `fetch` only **rejects** on **network-level** failure (DNS, offline, CORS, aborted).
  An HTTP **error response like 500/404 still resolves** with `res.ok === false`, so you
  must check `res.ok` and throw yourself, or you'll treat a server error as success.
- b) `AbortController` **actually cancels** the underlying request. `Promise.race` only
  ignores the slow promise — **the fetch keeps running in the background**, wasting
  bandwidth and potentially leaking. Hurts **performance/efficiency** (and reliability).
- c) A typed `TimeoutError` lets the `catch` branch on **`err instanceof TimeoutError`**,
  which is more robust than string-matching a message (messages get reworded/localized)
  and shows a clear class name in stack traces/devtools.
- d) Awaiting can take time; the element may be **removed from the DOM** before the
  promise settles. Writing to a **detached** component is wasted/erroneous work, so the
  guard skips the DOM update if `!this.isConnected`.

**7.**
- a) `transitions.success` only defines `{ reset: 'idle' }` — there's no `retry`, so
  `next` **returns `'success'` unchanged** and logs a warning; the UI **stays in
  success** and ignores the stray event. Desired because `retry` is only meaningful from
  `error`; the table makes the illegal transition a **no-op** instead of corrupting state.
- b) The current state is a **single value**, and the table enumerates the **only legal
  transitions** out of it. So contradictory combinations (loading *and* success) are
  **unrepresentable** — you can't be in two states at once. Invariant: exactly **one**
  valid state at a time, transitions only as declared.
- c) **Lesson 11 (Robustness).** Fix: **track the timer/in-flight work and cancel it in
  `disconnectedCallback`** (clear the `setTimeout` / `AbortController.abort()`), and guard
  with `isConnected` before any post-async DOM write.

**8.**
- a) **Happy path:** the change appears **instantly** (optimistic), then is silently
  reconciled with the server's version. **Failure path:** the optimistic change is
  **reverted** to `original` and a transient error toast ("Update failed…") shows.
- b) The optimistic copy is only your **guess**. A REST server may **normalize or add
  fields** (`updatedAt`, server-trimmed text, computed status). Reassigning from
  `serverTask` makes the UI match **server truth**; skipping it lets the UI silently
  **drift** from the backend.
- c) It prevents **concurrent/overlapping operations on the same task id** (double-toggle
  race). Protects **reliability** (consistency/data integrity) — and avoids confusing
  interleaved renders.

**9.** Any five+ of:
1. `var d` → use `const`/`let` (block scope).
2. `getUser` returns a promise it never `await`s and has **no error handling** — `await`
   inside `try/catch`.
3. **`http://`** → **`https://`** (tamper/eavesdrop risk).
4. **String-concatenated URL** with raw input → use `encodeURIComponent`/`URL` (injection,
   breakage on special chars).
5. **No `res.ok` check** before `res.json()` — a 500 is treated as success.
6. **`==`** → **`===`** (avoid coercion bugs).
7. **`el.innerHTML = user.name`** with untrusted data → **XSS**; use `textContent`
   (security).
8. **Magic numbers** `200` and `3000` → named constants (and `'200px'` directly).
9. Vague names (`u`, `d`) and duplicated fetch logic (DRY) — extract a helper.
10. No timeout/abort on the fetches (robustness).

**10.**
- `<div class="nav">` → **`<nav>`** — **accessibility** (and semantics/SEO).
- `<div class="link" onclick="go(...)">` → **`<a href="/home">`** or `<button>` — not
  keyboard-focusable/operable as a div → **accessibility**; the inline `onclick` also
  violates **separation of concerns** / CSP-friendliness (**security/maintainability**).
- `<img src="logo.png">` with **no `alt`** → add `alt` (or `alt=""` if decorative) —
  **accessibility**.
- Inline `style` with **magic hex colors** and **`#aaa` on `#bbb`** (poor contrast) →
  move to CSS classes/variables (**maintainability/DRY**) and fix the **contrast**
  (**accessibility**).

**11.**
- a) **Benefit:** full re-render is **simple and always correct** — UI can't drift from
  state. **Cost:** it **destroys and recreates DOM**, so **keyboard focus is lost** (and
  it's less efficient). "-ility" hurt: **usability** (focus/accessibility) and
  **performance efficiency**. Production would do targeted/vdom updates.
- b) **Graceful degradation** = build the full (often JS-rich) experience, then make sure
  it **fails *usefully*** when features are missing. **Progressive enhancement** = start
  from a working baseline (semantic HTML/server content) and **layer JS on top**. A
  `<noscript>` "JavaScript is required" that just errors out reflects (weak) **graceful
  degradation**, not progressive enhancement — the baseline doesn't actually work without
  JS.
- c) Closer to an **SPA** (one page, client-side view swapping, no full reloads).
  Trade-offs: SPA gives fast in-app navigation and shareable hash URLs but adds JS
  complexity, **fails without JS**, and (here) the router **owns the hash**, so in-page
  anchors collide; an MPA is simpler/more robust per page but does full reloads.

**12.**
- a) Agile = many **small, frequent, reversible** changes. That's only safe if each change
  is **automatically built, integrated, and tested fast** so failures are caught early and
  rollback is cheap — CI/CD provides that backstop, keeping complexity from "rolling
  downhill" into a big ball of mud.
- b) PRs give a **quality gate before code hits main**: catch design/maintainability
  issues, bugs/edge cases, missing tests, customer impact, and spread knowledge. **Large
  PRs** get weak ("lgtm") reviews because they're too much to reason about — reviewers
  skim, so keep PRs small.
- c) Any reasonable order, e.g.: **lint/format → unit tests → build/merge →
  integration/E2E tests → docs generation → optimization/bundling → deploy/deliver**
  (fast checks first, slow/broad ones later).

**13.**
- a) **Red** (write a failing test), **Green** (write the minimum code to pass),
  **Refactor** (clean up while staying green).
- b) It's **slow to start**, has a learning curve/skill cost, and feels unproductive
  ("writing tests before features"); teams rewarded for shipping skip it.
- c) LLM-generated code "**looks correct while being wrong**," so tests become the
  **verification boundary** you trust instead of the implementation; BDD keeps the checks
  tied to **user behavior** the team actually cares about. Write the spec/tests, let AI
  fill in, verify against them.

**14.**
- a) DI didn't make the *hard part* disappear — it made "which storage, how do we swap/
  test it" an **explicit, owned decision**. Hardcoding `localStorage` **hides** that
  decision: the difficulty is **relocated to later** (now you can't unit-test without a
  browser, can't swap to REST without rewriting the component). That deferred cost is
  **tech debt**. The reviewer still had to **understand DI, testability, and the
  abstraction boundary** to notice that the "working" code took on debt.
- b) **Easier:** producing a first draft of code/boilerplate, scaffolding, syntax. **Harder
  / more important:** **reviewing, verifying, understanding, and owning** it — judging
  trade-offs, spotting hidden compromises (pattern bias, missing `res.ok`, security),
  writing tests, and integrating it. Since AI can emit **lots** of plausible code fast and
  "**more code = more bugs**" (more surface area, dependencies, integration points), the
  bottleneck moves from *writing* to *critical evaluation* — the human's durable edge.

**15.**
- a) **Design Y (pub/sub + delegation).** Coupling: children **don't reference the hub**,
  so neither side hardcodes the other (loose coupling). **New publisher later:** just emit
  the same `notify` event — the hub needs **no change**. **Dynamically added children:**
  the hub's **one delegated listener** catches bubbled events from children added at any
  time. Design X hardwires every child to the hub (tight coupling, must re-wire each new
  child, hub must know each child).
- b) Any two, with reasons: **tests** (unit-test the hub's routing / a child's emit
  without the other — the decoupling makes this possible); an **ADR** (record why
  event-based over direct-call, since it shapes the architecture); **error handling /
  `res.ok`** if any fetch is involved; **listener cleanup** in `disconnectedCallback`
  (avoid leaks); **docs/JSDoc** on the event contract (`notify` + `detail` shape) so future
  publishers conform; **`composed: true`** so events escape shadow DOM.

**16.** *(Personalize — sample shapes.)*
- a) e.g. "We used **MVC + pub/sub**: components emit events, a controller updates a
  model that other views subscribe to."
- b) e.g. "**Reliability/availability** drove us to add `res.ok` checks, timeouts, and an
  offline fallback," or "**accessibility** drove semantic HTML + `alt` text + keyboard
  support."
- c) e.g. "Replaced a giant `if/else` with a **Strategy** object," or "added **JSDoc** so
  docs generate from code and stop going stale," or "wrote an **ADR** for choosing hash
  routing."

---

*Source patterns: pub/sub (01), event delegation (02), components/decomposition (04),
MVC (05/06), strategy (07), dependency injection (08), state machine (10), robustness
(11), CRUD service facade (13), routing (14). Concepts: cohesion/coupling, SRP,
separation of concerns, DRY, encapsulation, abstraction, DI, minimalism, client-server &
the HTML/CSS/JS layered model, graceful degradation vs progressive enhancement, SPA vs
MPA, CRUD, REST, MVC, web components, code smells (magic numbers, XSS via innerHTML,
`==`, `http`, div-soup, contrast), CI/CD & pull requests, build pipeline, testing pyramid,
TDD/BDD, software lifecycle/health, ADR & JSDoc documentation, and GenAI's effect on tech
debt and where SWE difficulty lives.*
