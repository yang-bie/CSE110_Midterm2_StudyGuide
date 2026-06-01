# CSE 110 Midterm 2 (Practice — Variant 6: CODE-FOCUSED) — Powell Spring 2026

Name: ______________________________   Score: ______ / 120

PID: ______________________   Team #: ______

> Same 60/40 split as Variants 4–5. This one closes out the **rest of the code** not yet
> drilled: the decomposition components (theme-picker, article-viewer, app-level debug
> logging), the alt pub-sub/delegation demos (color-picker state change, dynamic tiles),
> the Todo **view** components, the Sortable-Storable capstone, the Robustness test-bench
> + global handlers, and the Routing **views** (light DOM vs shadow DOM, where view state
> lives). Answer Key at the bottom.

---

## Part A — Reading & Reasoning About Patterns [34 pts]

**1. [6 pts] Spot the listener-cleanup bug (theme-picker).**

```js
constructor() {
  super();
  this.attachShadow({ mode: 'open' });
  this.systemPrefersDark = window.matchMedia('(prefers-color-scheme: dark)');
  this.systemPrefersDark.addEventListener('change', this.handleSystemPreference.bind(this)); // (A)
}
disconnectedCallback() {
  this.langObserver.disconnect();
  this.systemPrefersDark.removeEventListener('change', this.handleSystemPreference);          // (B)
}
```

a) **[4]** Lines (A) and (B) are supposed to add and remove the **same** listener, but the
removal **silently does nothing**. Why? (Hint: compare the two function references.)

<br>

b) **[2]** Show the fix, and name the pattern the pub-sub lessons used to get this right
(they wrote `this.handleMessage = this.handleMessage.bind(this)` once in the constructor).

<br>

**2. [6 pts] Dynamic event delegation.**

```js
document.getElementById('tiles').addEventListener('tile-clicked', (event) => {
  const tile = event.target.closest('tile-component');
  if (!tile) return;
  log.textContent = `Last clicked: ${event.detail.label}`;
});

document.getElementById('add-tile-btn').addEventListener('click', () => {
  const newTile = document.createElement('tile-component');
  newTile.setAttribute('label', randomName);
  document.getElementById('tiles').appendChild(newTile);   // NOTE: no listener added to newTile
});
```

a) **[3]** Clicking a freshly-added tile still updates the log even though **no listener
was attached to it**. Explain the mechanism that makes this work.

<br>

b) **[3]** Rewrite the "non-delegated" version in words (what would you have to do every
time you add a tile?), and name the **code smell** that version creates.

<br>

**3. [8 pts] App-level logging = pub/sub + delegation.** Each component emits debug events;
`main.js` wires the app:

```js
// inside theme-picker / article-viewer:
this.dispatchEvent(new CustomEvent('debug-log', {
  bubbles: true, composed: true,
  detail: { source: 'theme-picker', type, message, data },
}));
// main.js:
document.body.addEventListener('debug-log', (event) => {
  debugLog.addEntry(event.detail);          // ONE listener for ALL components
});
```

a) **[3]** How many listeners does the app attach for logging, no matter how many
components emit `debug-log`? Why does this scale, and what does `event.detail.source` do?

<br>

b) **[3]** Name **two** "-ilities" this decoupled logging buys (think: adding a new
component later; testing a component without the logger present).

<br>

c) **[2]** The `article-viewer` watches for language changes with a `MutationObserver` on
`<html lang>` and calls `this.observer.disconnect()` in `disconnectedCallback()`. Why is
that `disconnect()` important?

<br>

**4. [6 pts] Pub/Sub that changes another component's state (color picker).**

```js
// predefined buttons:  <button data-color="#ffb3ba">Pastel Red</button>
this.shadowRoot.querySelectorAll('button').forEach(btn =>
  btn.addEventListener('click', (e) => this.publishColor(e.target.dataset.color)));
// custom input:
this.shadowRoot.querySelector('#custom-color').addEventListener('input', (e) =>
  this.publishColor(e.target.value));
// publish:
this.dispatchEvent(new CustomEvent('color-changed', {
  detail: { color: newColor }, bubbles: true, composed: true }));
```

a) **[3]** The display component sets `this.style.backgroundColor = event.detail.color`.
The picker and display have **no direct reference** to each other — so how does the picker
change the display's state? Which "-ility" does this loose coupling improve?

<br>

b) **[3]** The custom color uses the **`input`** event, not `click` or `change`. What's the
UX difference, and why is `input` the right choice for a live color preview?

<br>

**5. [8 pts] Light DOM vs Shadow DOM — the routing views.**

```js
// list-view.js / preferences-view.js — NO attachShadow():
class ListView extends HTMLElement {
  connectedCallback() { this.innerHTML = `<h2>List view</h2> ...`; }   // light DOM
}
// preferences-view.js persists its setting in localStorage:
const current = localStorage.getItem(STORAGE_KEY) ?? 'light';
// ...on change: localStorage.setItem(STORAGE_KEY, e.target.value);
```

a) **[3]** The widget components (lessons 10–13) use `attachShadow`, but these **top-level
views deliberately do not**. Give the trade-off: what does shadow DOM give a widget, and
why would it *hurt* a full-page view?

<br>

b) **[3]** The router **destroys and recreates** a view on every navigation. So why does
the preferences view store the theme in `localStorage` instead of an instance field?

<br>

c) **[2]** The lesson applies the saved theme via an **inline `<script>` in `<head>`**
before the module runs, "to avoid FOUC." What is FOUC, and why does doing it early fix it?

<br>

---

## Part B — Robustness, State, Async [26 pts]

**6. [10 pts] Todo view components (the V in MVC).**

```js
// todo-input.js (constructor):
form.addEventListener('submit', (e) => {
  e.preventDefault();
  const text = input.value;
  if (!text.trim()) return;                                   // (★)
  this.dispatchEvent(new CustomEvent('intent', {
    bubbles: true, composed: true, detail: { action: 'add', text }}));
  input.value = ''; input.focus();
});
// todo-list.js (constructor):
this.shadowRoot.addEventListener('click', (e) => {
  const action = e.target.dataset.action;
  const id = Number(e.target.dataset.id);
  if (!action || !id) return;
  this.dispatchEvent(new CustomEvent('intent', {
    bubbles: true, composed: true, detail: { action, id }}));
});
```

a) **[3]** Both views dispatch an **`intent`** event instead of calling the model. State
the MVC contract: what does the View know about the Model, and who turns `intent` into a
model call?

<br>

b) **[3]** Both listeners are attached in the **constructor**, and `todo-list` uses **one**
delegated click listener for *all* toggle/remove buttons. Why constructor (not
`connectedCallback`), and why one listener instead of one-per-button?

<br>

c) **[2]** The `todo-list` comment admits: re-rendering replaces the list's innerHTML, so
"clicking a checkbox destroys the checkbox you just clicked and focus jumps to `<body>`."
Name the "-ility" hurt and one production fix.

<br>

d) **[2]** The list template escapes `"` → `&quot;` before putting `text` into a
`todo-item text="..."` attribute. What attack/bug does that prevent, and what's its
**limitation** (it only escapes quotes)?

<br>

**7. [8 pts] Sortable-Storable capstone (Strategy + DI, two axes).**

```js
static get observedAttributes() { return ['strategy', 'storage']; }
attributeChangedCallback(name, oldValue, newValue) {
  if (!this.isConnected || oldValue === newValue) return;     // (◆)
  if (name === 'storage')  this.swapStorage(newValue);
  if (name === 'strategy') this.render();
}
swapStorage(type) {
  this._storageInstance = StorageFactory.createStorage(type);
  this._items = this._storageInstance.load();                 // (●)
  this.render();
}
sortedItems() { return [...this._items].sort(strategies[this.strategy] ?? strategies.byNameAsc); }
```

a) **[3]** Changing `strategy` only re-renders; changing `storage` swaps the backend.
Explain why these are **independent axes** and which principle that expresses
(orthogonality / SRP / separation of concerns).

<br>

b) **[3]** At (●), swapping storage calls `load()` on the new backend — it does **NOT**
migrate items from the old one. Is that a bug or a documented design choice? What
"-ility"/quality question would you ask the product owner here?

<br>

c) **[2]** What two things does the guard at ◆ prevent (`!this.isConnected` and
`oldValue === newValue`)?

<br>

**8. [8 pts] Robustness — the test-bench + global safety net.**

```js
// test-bench.js — buttons deliberately misbehave:
else if (action === 'error') {
  throw new Error('Simulated synchronous runtime error');        // uncaught SYNC throw
} else if (action === 'unhandled-rejection') {
  Promise.reject(new Error('Simulated unhandled promise rejection')); // no .catch()
}
// main.js — last-resort global handlers:
window.addEventListener('error',  (e) => report(e.error));
window.addEventListener('unhandledrejection', (e) => report(e.reason));
```

a) **[3]** Which global handler catches the **sync throw**, and which catches the
**promise rejection**? Why do you need *both* (one wouldn't cover the other)?

<br>

b) **[3]** The lesson calls these the "**last line of defense**." What does that mean —
when does code reach these handlers, and what would production do here besides log?

<br>

c) **[2]** These global nets are good practice, but relying on them *instead of* local
`try/catch` / `res.ok` is bad. Why?

<br>

---

## Part C — Smells, Trade-offs, Process, GenAI, Scenario [60 pts]

**9. [10 pts] Spot the smells (JS).** Review this AI-written snippet. Identify **at least 5
distinct** problems and a fix for each.

```js
const KEY = 'session';
function save(obj) { localStorage.setItem(KEY, obj); }
async function getUsers() {
  const r = await fetch(API + '/users');
  return r.json();
}
function render(users) {
  let html = '';
  for (var i = 0; i < users.length; i++) {
    html += '<div onclick="pick(' + i + ')">' + users[i].name + '</div>';
  }
  document.body.innerHTML = html;
}
```

<br><br>

**10. [6 pts] Spot the smells (HTML/CSS).** Identify the problems, give fixes, and name the
**"-ility"** hurt by each.

```html
<table>
  <tr><td><img src="hero.jpg" width="2000"></td></tr>
  <tr><td><font color="blue">Welcome</font></td></tr>
</table>
<a href="#" onclick="nav()">click here</a>
```

<br><br>

**11. [8 pts] Trade-offs & the web platform.**

a) **[3]** Shadow DOM gives a widget **style/markup encapsulation**, but the routing views
skip it so the **global stylesheet reaches them**. Give one benefit and one cost of shadow
DOM, and a rule of thumb for **widget vs. page-level view**.

<br>

b) **[3]** `article-viewer` reacts to `<html lang>` changes with a **`MutationObserver`**
rather than a prop/attribute the parent sets. Give one advantage and one risk of observing
external DOM like that.

<br>

c) **[2]** The Todo template does `escapeAttr(text)` before interpolating user text into
HTML. State the general rule about **trusting user input** (the security "golden rule").

<br>

**12. [8 pts] Process & documentation.**

a) **[3]** The pattern code is heavily **JSDoc**-commented (`@class`, `@fires`, `@param`).
What does JSDoc let a team *do*, and how does that fight the "docs go stale" problem?

<br>

b) **[3]** Name three things you'd put in a project's **pre-commit / `.gitignore` /
EditorConfig** setup to "code as one team," and why each helps.

<br>

c) **[2]** When you make a hard-to-reverse architectural call (e.g. "all widgets use shadow
DOM; views use light DOM"), what artifact records the decision and its rationale?

<br>

**13. [6 pts] TDD & BDD.**

a) **[3]** Which pieces of this codebase are **easiest to unit-test**, and why:
`strategies.byNameAsc`, `next(state, event)`, or a full web component's rendered DOM? Tie
to **pure functions vs. DOM**.

<br>

b) **[3]** Write a **BDD** Given/When/Then for the Todo app's "add an empty todo is
ignored" behavior (the `if (!text.trim()) return` guard).

<br>

**14. [8 pts] GenAI reasoning.**

a) **[4]** An LLM generated a web component that adds a `window`/`matchMedia` listener but
**omits `disconnectedCallback`**. It passes the demo. Explain the latent bug, why it's
invisible in a quick demo, and how it shows GenAI **shifting difficulty** from *writing*
to *reviewing* code.

<br>

b) **[4]** Using **trade-off thinking**: the codebase favors **simple, readable** patterns
(full re-render, one delegated listener) over clever micro-optimizations. Why is
"favor reading over writing" *more* important when an LLM can emit large volumes of code
fast? Tie to **tech debt** and team maintainability.

<br>

**15. [8 pts] Pick-between capstone.** A teammate's PR adds a settings widget two ways:

> **Design J:** `class Settings extends HTMLElement { connectedCallback(){ this.innerHTML
> = ... } }` — **light DOM**, styled by the global stylesheet, IDs like `#save`.
>
> **Design K:** same widget with `attachShadow({mode:'open'})` and component-scoped
> `<style>`.

a) **[4]** This is a **reusable widget dropped into many pages**. Which design reduces
style collisions and why? When would Design J (light DOM) actually be the better call?

<br>

b) **[4]** Before approving, name **two** things you'd require regardless of choice
(listener cleanup in `disconnectedCallback`, `composed: true` if it emits events out of a
shadow root, an ADR, accessible labels) and why each matters.

<br>

**16. [6 pts] Your team / project.** (Easy points if you've been involved.)

a) **[2]** Where does (or could) your project use **web components**, and did you use shadow
DOM or light DOM — why?

b) **[2]** Name one **cleanup / lifecycle** concern you handled (or should) — e.g. removing
a listener, clearing a timer/observer — and the bug it prevents.

c) **[2]** Name one **"-ility"** that shaped a UI or architecture decision on your project.

<br>

---
---

# ANSWER KEY

> Model answers — the key points a grader looks for. Wording can differ.

**1.**
- a) `this.handleSystemPreference.bind(this)` creates a **brand-new function object** every
  time it's called. Line (A) adds *that* new bound function; line (B) passes
  `this.handleSystemPreference` (the **original unbound** method) — a **different
  reference**. `removeEventListener` only removes a listener if you pass the **exact same
  function** that was added, so it removes **nothing**. The `matchMedia` object keeps the
  bound handler alive → the detached component can't be GC'd and the handler keeps firing
  → **memory leak**.
- b) Bind **once** and reuse the saved reference:
  ```js
  constructor(){ ...; this._onSysPref = this.handleSystemPreference.bind(this);
                 this.systemPrefersDark.addEventListener('change', this._onSysPref); }
  disconnectedCallback(){ this.systemPrefersDark.removeEventListener('change', this._onSysPref); }
  ```
  That's exactly what the pub-sub `ColorDisplay`/`Subscriber` did with
  `this.handleMessage = this.handleMessage.bind(this)`.

**2.**
- a) The listener is on the **parent `#tiles`**, not the tiles. A click on any tile fires a
  `tile-clicked` event that **bubbles up** to `#tiles`, where the single delegated listener
  handles it — including tiles created **after** the listener was attached, because
  bubbling doesn't care when the child was added.
- b) Non-delegated: every time you `createElement('tile-component')` you'd also have to
  `newTile.addEventListener('tile-clicked', …)` (and remove it later). Smell: **repeated
  per-element listener setup / re-binding on every render** — doesn't scale and leaks if
  you forget cleanup.

**3.**
- a) **One** listener (on `document.body`), regardless of how many components emit. It
  scales because every component's `debug-log` event **bubbles up** (and `composed` lets it
  escape each shadow root) to that single handler. `event.detail.source` tells the logger
  **which component** sent it, so it can route/label without referencing the component.
- b) Any two: **Maintainability/Modularity** (add a new component that emits `debug-log` and
  the logger needs **zero changes**), **Testability** (a component can be tested without the
  logger wired up — it just fires an event nobody has to catch), **Reusability/loose
  coupling** (components don't depend on the logger's API).
- c) A `MutationObserver` holds a reference and keeps **observing the document** even after
  the component is removed. Without `disconnect()`, that's a **leak** (and wasted work
  re-rendering a detached component) — same cleanup discipline as removing event listeners.

**4.**
- a) The picker **dispatches** a `color-changed` event (bubbles + composed) that the
  display **subscribes** to on `document`; the display reads `event.detail.color` and
  updates its own state. Neither holds a reference to the other — that's **pub/sub**. It
  improves **maintainability / reusability** (loose coupling): either component can be
  swapped or reused independently, and multiple displays could subscribe.
- b) `input` fires **continuously as the user drags** the color picker; `change` fires only
  when they **commit** (close the picker / blur). For a **live preview** you want the
  display to update in real time, so `input` is correct; `click` wouldn't give the chosen
  value at all.

**5.**
- a) Shadow DOM gives a **widget** encapsulation: scoped styles and markup that won't leak
  out or be clobbered by the page — great for reusable, self-contained components. For a
  **full-page view** that's a *cost*: the page's **global stylesheet can't cross the shadow
  boundary**, so every view would have to re-declare typography/links/code styles. Rule of
  thumb: **shadow DOM for widgets, light DOM for page-level views**.
- b) Because the router **destroys the view instance** on every navigation, an instance
  field would be **wiped** when you leave and come back. `localStorage` (a store that
  outlives the component) is the right home for **state that must persist across
  navigations** — the view reads it on re-creation.
- c) **FOUC = Flash Of Unstyled (or wrong-theme) Content** — a brief moment where the page
  renders with the default theme before JS applies the saved one. Applying the theme in an
  **inline head script** runs it **before first paint**, so the correct theme is set from
  the very first render.

**6.**
- a) The View knows **nothing** about the Model — it only announces user **intent**
  (`{action, text/id}`). The **Controller** subscribes to `intent` and translates it into
  `model.add/toggle/remove`. (Then the model emits `todos-changed`, which the controller
  pushes back to `list.todos`.)
- b) **Constructor** runs **once** per element, so the listener isn't **duplicated** each
  time the element re-attaches to the DOM (`connectedCallback` can fire multiple times).
  **One delegated listener** handles toggle/remove for any number of items (and items added
  later) — attaching one-per-button would re-bind on every render and not scale.
- c) **Usability/accessibility** (keyboard **focus is lost** on full re-render). Production
  fix: **targeted/keyed DOM updates** (diffing, `MutationObserver`, or a lib like Lit) to
  preserve focus instead of replacing innerHTML.
- d) Escaping `"` stops the `text` from **breaking out of the attribute** (`text="..."`) —
  an attribute-injection / mini-XSS vector. **Limitation:** it only handles quotes, not
  `<`, `>`, `&`, etc., so it's **not** full HTML sanitization — fine for this constrained
  spot, not a general-purpose escape.

**7.**
- a) `strategy` (how to **sort**) and `storage` (where data **lives**) are unrelated
  responsibilities that share no code path, so each can change without touching the other —
  **orthogonality / SRP / separation of concerns**. The Strategy lookup and the injected
  storage are the two independent axes.
- b) It's a **documented design choice** (the comment says backends are independent and
  switching does not migrate). The quality question for the PO: is **data portability /
  expected persistence** a requirement? Users might expect their items to follow them when
  switching backends — that's a **usability/requirements** decision, not a code default.
- c) `!this.isConnected` avoids reacting to attribute changes **before the element is in the
  DOM** (e.g. while being set up); `oldValue === newValue` avoids a **redundant
  re-render/swap** when the value didn't actually change.

**8.**
- a) `window.addEventListener('error', …)` catches the **uncaught synchronous throw**;
  `window.addEventListener('unhandledrejection', …)` catches the **promise rejection with
  no `.catch()`**. You need both because they're **different channels** — a rejected promise
  does **not** trigger the `error` event, and a sync throw doesn't trigger
  `unhandledrejection`.
- b) "Last line of defense" = the event only reaches them when **nothing else handled it**
  (no local `try/catch`, no `.catch()`). In production you'd **forward the error to a
  tracking service** (Sentry/Rollbar/Datadog), show a degraded-mode UI, or persist a
  breadcrumb — not just `console.log`.
- c) Global handlers are **coarse** — they fire after the failure, with little context, and
  can't recover the specific operation. Local `try/catch` and `res.ok` checks let you
  **handle the failure where it happened** (retry, revert, show a field error), so the user
  gets a meaningful outcome instead of a generic banner.

**9.** Any five+ of:
1. `localStorage.setItem(KEY, obj)` stores an **object** → becomes `"[object Object]"`; use
   `JSON.stringify(obj)` (and `JSON.parse` on read).
2. `getUsers` has **no `res.ok` check** and no error handling → a 500 is treated as data.
3. **`var i`** in the loop → use `let`.
4. **String-built HTML** with `users[i].name` → **XSS**; build DOM nodes / use
   `textContent`, or escape.
5. **Inline `onclick="pick(i)"`** → separation of concerns / CSP; use delegated listeners.
6. `<div onclick=...>` as a **button** → not keyboard-accessible; use `<button>`.
7. **`document.body.innerHTML = html`** wipes the **entire page** (destroys other
   listeners/focus) → render into a scoped container.
8. **String-concatenated URL** (`API + '/users'` is okay, but `pick(' + i + ')` style and
   any id concat) → prefer template/encoding; missing timeout/abort on fetch (robustness).

**10.**
- `<table>` used for **layout** → use semantic elements + CSS (`<section>`/fl/grid) —
  **accessibility/maintainability** (screen readers announce a data table).
- `<img src="hero.jpg" width="2000">` with **no `alt`** and a huge fixed width → add `alt`,
  use responsive sizing/optimized image — **accessibility** + **performance**.
- `<font color="blue">` is a **deprecated/non-semantic** tag → style with CSS —
  **maintainability** (and standards/compatibility).
- `<a href="#" onclick="nav()">click here</a>` → use a real `href` or a `<button>`, and
  **descriptive link text** instead of "click here" — **accessibility/usability** (and the
  inline handler hurts maintainability/security).

**11.**
- a) **Benefit:** shadow DOM **scopes styles/markup** so a reusable widget won't clash with
  or be clobbered by the page. **Cost:** the **global stylesheet can't reach inside**, so
  shared typography/themes must be re-provided (via parts/vars). Rule of thumb: **widgets →
  shadow DOM** (encapsulation), **page-level views → light DOM** (inherit global styles).
- b) **Advantage:** the component **auto-reacts** to external state (lang) without the
  parent wiring a prop — loose coupling to the page. **Risk:** it reaches **outside its own
  boundary** (observing `document`), which is a hidden dependency, can fire often, and
  **must be cleaned up** (`disconnect()`) or it leaks.
- c) **Golden Rule of Web Security:** you **cannot trust users or their input** — treat all
  inputs (and outputs) as untrusted; escape/validate/sanitize before using them in HTML,
  URLs, storage, etc.

**12.**
- a) JSDoc lets you **generate documentation from the code itself** (and powers editor
  tooltips/type hints via `@param`/`@returns`). Because the docs live **next to the code**,
  they're far more likely to be updated with it — fighting the "docs drift away from code"
  problem (vs docs in a separate wiki nobody updates).
- b) Any three: **linter/formatter** (consistent style, catch errors before commit),
  **EditorConfig** (consistent indentation/encoding across editors), **`.gitignore`**
  (keep `.DS_Store`/`node_modules`/secrets out of the repo), **pre-commit hooks** (run
  lint/tests automatically so problems don't enter the repo). Each makes the standard
  **automatic** instead of personal → "code as one team."
- c) An **ADR (Architecture Decision Record)** — captures the decision, context,
  alternatives, and *why*, since it's costly to reverse later.

**13.**
- a) `strategies.byNameAsc` and `next(state, event)` are **pure functions** (input →
  output, no DOM/side effects) → trivial to unit-test (`expect(next('idle','fetch')).toBe
  ('loading')`). A full component's **rendered DOM** needs a DOM environment, is slower and
  more fragile → fewer, higher-level tests. (Testing-pyramid logic: push tests down to the
  pure functions.)
- b) e.g. **Given** the todo input is empty (or only whitespace), **When** the user clicks
  Add / presses Enter, **Then** no `intent` event is dispatched and no todo is added (the
  list is unchanged).

**14.**
- a) The component registers a `matchMedia`/`window` listener (or observer) but never
  removes it, because there's **no `disconnectedCallback`** → when the element is removed
  the listener **leaks** (and keeps firing on a detached node). It's invisible in a demo
  because you **never remove the component**, so the leak/duplicate-handler never manifests
  — you'd only see it after navigation/teardown or in a long-running app. The bug lives in
  what the AI **didn't write**, so catching it requires a reviewer who **knows the
  lifecycle** — difficulty shifts from *writing* code to *reviewing/verifying* it.
- b) Code is **read far more than written**, and AI lets you generate **lots** of it fast —
  so the bottleneck and risk move to **reading, reviewing, and maintaining**. Clever,
  compact, or over-optimized AI code that no one understands becomes **tech debt** the team
  can't safely change. Simple, readable patterns (full re-render, one delegated listener)
  keep the code **reviewable and maintainable**, which matters more, not less, as volume
  grows.

**15.**
- a) **Design K (shadow DOM)** reduces style collisions: scoped `<style>` means the
  widget's CSS won't leak onto the host page and the page's CSS won't accidentally restyle
  it — important for a **drop-in reusable widget**. **Design J (light DOM)** is better when
  you *want* the widget to **inherit the page's theme/typography**, need it styled by the
  global stylesheet, or need maximum simplicity / no shadow-DOM gotchas (e.g. forms,
  global CSS frameworks).
- b) Any two with reasons: **listener/timer/observer cleanup in `disconnectedCallback`** (no
  leaks); **`composed: true`** on any event it needs to emit out of a shadow root (or the
  parent never hears it); **accessible labels / semantic controls** (usability); an **ADR**
  recording the shadow-vs-light choice (it's architectural and easy to get inconsistent).

**16.** *(Personalize — sample shapes.)*
- a) e.g. "We built widgets as web components with **shadow DOM** for style isolation, but
  page-level views in **light DOM** so the global theme applies."
- b) e.g. "We remove a `resize`/`matchMedia` listener (and clear a polling timer) in
  `disconnectedCallback` to avoid a leak / handlers firing on detached nodes."
- c) e.g. "**Accessibility** drove semantic HTML + labels + a live region," or
  "**reliability** drove `res.ok` + retry + offline fallback."

---

*Source code now covered (in addition to V4/V5): pub-sub state-change/color-picker (01a),
dynamic event delegation (02a), components-and-decomposition theme-picker/article-viewer +
app-level debug-log delegation (04), Todo view components todo-input/todo-list +
escapeAttr template (06), MVC/Todo main.js wiring (05/06), Sortable-Storable capstone two-
axis Strategy+DI (09), Robustness test-bench + window error/unhandledrejection handlers
(11), Routing list/preferences views — light vs shadow DOM, localStorage persistence, FOUC
(14). Concepts: listener/observer/timer cleanup & the bind-reference bug, event delegation
for dynamic elements, pub/sub for cross-component state, shadow vs light DOM trade-offs,
MutationObserver, MVC view contract, orthogonality/SRP, global error handling, JSDoc &
ADR documentation, escaping/XSS, code smells, TDD/BDD on pure functions, and GenAI's
shift of difficulty from writing to reviewing.*
