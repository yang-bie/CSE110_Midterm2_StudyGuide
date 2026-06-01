# CSE 110 Pattern Code — Full Walkthrough

A plain-English description of **all** the lecture pattern code:
`sample-pattern-code` (lessons 01–06) and `Batch-2-of-patterns` (lessons 07–14).

The goal isn't to memorize lines — it's to know, for each example: **what problem it
solves, who knows about whom, what can change independently, and the one "gotcha" the
lesson plants.**

---

## Conventions used in (almost) every example

- **Web Components.** Each UI piece is a custom element: `class X extends HTMLElement` +
  `customElements.define('x-name', X)`. Lifecycle hooks: `constructor` (runs once),
  `connectedCallback` (on insert, can fire repeatedly), `disconnectedCallback` (on
  removal), `attributeChangedCallback` (when an `observedAttributes` value changes).
- **File split (decomposition).** Bigger components separate concerns into files:
  `name.js` (logic) · `name-template.js` (HTML string) · `name-styles.js` (CSS string) ·
  `name-i18n.js` (translations).
- **`main.js` = entry point.** Imports the components (so the browser registers them
  before parsing the HTML) and, inside `DOMContentLoaded`, wires things together.
- **`render()`** usually does `this.shadowRoot.innerHTML = `<style>${styles}</style>${template(...)}``
  — a **full re-render** on every change. Simple and always correct, but it **destroys
  DOM and loses keyboard focus** (noted as a teaching trade-off throughout).
- **Listeners attached in the `constructor` (once), via delegation.** Attaching in
  `connectedCallback` instead would **duplicate** the listener every time the element
  re-attaches. Most components put one delegated listener on `this.shadowRoot` and branch
  on `e.target.dataset.action` / `dataset.role`.
- **Shadow DOM vs Light DOM.** `attachShadow({mode:'open'})` scopes styles/markup — used
  for **widgets**. Some elements deliberately use **light DOM** (`this.innerHTML`, no
  shadow) so global CSS reaches them or so event delegation by `event.target` keeps
  working (see Todo item & routing views).

---

## 01 / 01a — Pub/Sub  (`sample-pattern-code`)

**Idea:** one component announces "something happened"; others react. The publisher holds
**no reference** to subscribers → loose coupling.

- **`01-pub-sub.html`** — a `<publisher-component>` with a text input + button, and a
  `<subscriber-component>` that displays the message. On click the publisher dispatches
  `new CustomEvent('custom-message', { detail:{msg}, bubbles:true, composed:true })`. The
  subscriber listens on `document` and updates its text.
  - **Gotchas taught:** `composed: true` is required for the event to **escape the shadow
    DOM**; `bubbles: true` lets it travel up to `document`. The subscriber stores
    `this.handleMessage = this.handleMessage.bind(this)` once and **removes the listener
    in `disconnectedCallback`** to prevent a memory leak.
- **`01a-pub-sub.html`** — same pattern, but the publisher (**color picker**) changes the
  **visual state** of the subscriber (**color display**). Predefined buttons carry
  `data-color`; a custom `<input type="color">` uses the **`input`** event (fires live as
  you drag, vs `change` which only fires on commit). Display sets
  `this.style.backgroundColor = event.detail.color`.

---

## 02 / 02a — Event Delegation  (`sample-pattern-code`)

**Idea:** put **one** listener on a parent instead of one per child. Children's events
bubble up; the parent identifies the source via `event.target.closest(...)` and reads
`event.detail`.

- **`02-event-delegation.html`** — three `<tile-component>`s inside `#tiles`. Each tile
  (light DOM) dispatches `tile-clicked` (`bubbles:true`). A single listener on `#tiles`
  logs `event.detail.label`. Comment highlights: the parent **never reaches into child
  internals**, it only reads `event.detail`; `closest()` is robust to clicks landing on
  inner elements but does couple slightly to HTML structure.
- **`02a-event-delegation.html`** — adds an **"Add Random Tile"** button. New tiles are
  appended with **no listener attached to them**, and clicking them still works — because
  the delegated listener lives on the parent and bubbling doesn't care when the child was
  added. This is the headline benefit of delegation.

---

## 03 — Notifications Hub Capstone  (`sample-pattern-code`)

**Combines** pub/sub (01) + delegation (02).

- **`status-publisher.js`** — a small form (level `<select>` + message input). On "send"
  it dispatches `notify` (`bubbles`+`composed`) with
  `detail:{ source, level, message, timestamp }`. Empty message → `DEFAULT_MESSAGES[level]`
  (uses `||`, which correctly falls through on an empty string). `source` comes from an
  attribute and is forwarded verbatim.
- **`notifications-hub.js`** — hosts any number of publishers via a `<slot>`. Attaches
  **one** `notify` listener on itself (`handleNotify.bind(this)`). Routes each event into a
  per-source stack in a `Map`, using **only `event.detail`** (never querying children).
  Caps each stack at `MAX_PER_SOURCE = 5` (drops oldest). `renderStacks()` updates **only**
  the `#stacks-grid` sub-region, leaving the slotted publishers untouched — a **targeted
  update** example (contrast with full re-render).
  - **Gotcha:** `if (!source) return` drops malformed events. Adding a new publisher later
    needs **zero hub changes**.
- **`main.js`** — just imports both elements to register them.

---

## 04 — Components & Decomposition  (`sample-pattern-code`)

**Idea:** split a feature into focused components and split each component into
logic/template/styles/i18n files. Components communicate by **events**, not references.

- **`theme-picker.js`** — a `<select>` to switch light/dark. Reads/writes
  `localStorage('patterns:04:theme')`, respects the OS setting via
  `window.matchMedia('(prefers-color-scheme: dark)')`, sets `data-theme` on `<html>`, and
  dispatches `theme-changed`. Also watches `<html lang>` with a `MutationObserver` to
  re-render translated labels.
  - **⚠️ The planted bug (used in Variant 6):** the constructor does
    `addEventListener('change', this.handleSystemPreference.bind(this))` but
    `disconnectedCallback` does `removeEventListener('change', this.handleSystemPreference)`.
    `.bind()` creates a **new function each call**, so the removal targets a **different
    reference** and removes **nothing** → the `matchMedia` listener leaks. Correct pattern:
    bind once, store the reference, use it for both add and remove.
- **`lang-picker.js`** — sibling of theme-picker for language. Persists
  `patterns:04:lang`, sets `<html lang>` + `dir` (`rtl` for `ar`), re-renders on lang
  change, emits `debug-log`. (Cleans up its observer correctly in `disconnectedCallback`.)
- **`article-viewer.js`** — presentational; observes `<html lang>` and re-renders its
  translated content. Properly `observer.disconnect()`s on removal.
- **`debug-log.js`** — a **sink** component with `addEntry(detail)` that appends a row and
  auto-scrolls. Knows nothing about who logs.
- **`main.js`** — the key wiring: **one** delegated `debug-log` listener on `document.body`
  routes *every* component's debug event to the single `<debug-log>` by reading
  `event.detail.source`. Decoupled, app-level pub/sub + delegation.

---

## 05 — MVC  (`sample-pattern-code`)

**Idea:** Model (data/rules) ↔ View (UI) ↔ Controller (bridge); Model and View never
reference each other.

- **`model.js`** — `CounterModel extends EventTarget` with a **private** `#value`. Methods
  `increment/decrement/reset` mutate it and `#emit()` a `value-changed` event. Extending
  `EventTarget` gives `addEventListener`/`dispatchEvent` for free; the Model **never
  touches the DOM**.
- **`counter-view.js`** — renders a number + buttons. On click it dispatches an `intent`
  event `{action}` (delegated listener wired in the **constructor**). When its `value`
  attribute changes (`observedAttributes`/`attributeChangedCallback`), it re-renders. The
  View **knows nothing** about the Model.
- **`controller.js`** — `connect(model, view)`: maps `intent` → model method, and
  `value-changed` → `view.setAttribute('value', …)`. Guards against double-wiring
  (`_mvcConnected`).
- **`main.js`** — `new CounterModel()`, grab the view, `connect`, set initial value.

---

## 06 — Todo-MVC Capstone  (`sample-pattern-code`)

Full MVC + components + delegated events + model events.

- **`model.js`** — `TodoModel extends EventTarget`, private `#todos`/`#nextId`. `add` trims
  and **ignores empty** input; `toggle`/`remove` mutate; all emit `todos-changed` carrying
  a **copy** (`list()` returns `[...#todos]`) so callers can't mutate internal state
  (encapsulation).
- **`todo-input.js`** (View) — a form; on submit (Enter or button) it dispatches `intent
  {action:'add', text}`, then clears + refocuses. Listener wired in constructor; knows
  nothing about the Model.
- **`todo-list.js`** (View) — renders the list and uses **one delegated click listener**
  for all toggle/remove buttons, dispatching `intent {action, id}` (id from
  `dataset.id`). Comment notes the full re-render **loses focus** on the clicked checkbox.
- **`todo-item.js`** — **purely presentational, light DOM, no handlers.** Uses light DOM on
  purpose: if it used Shadow DOM, the browser would **retarget `event.target`** to the host
  when clicks cross the boundary, breaking the parent's delegation-by-`dataset`. Its
  template escapes `"`→`&quot;` so text can't break out of the `text="…"` attribute (a
  mini-XSS guard, but **only** quotes, not full sanitization).
- **`controller.js`** — `connect(model, input, list)`: one shared `handleIntent` serves
  both views (branches on `action`), and `todos-changed` pushes `list.todos = …`.
- **`main.js`** — builds model, grabs the two views, `connect`, initial render.

---

## 07 — Strategy  (`Batch-2-of-patterns`)

**Idea:** swap interchangeable behavior selected at runtime; all options share one
signature.

- **`strategies.js`** — a `strategies` object of four comparators, all
  `(a,b)=>number` (`byNameAsc/Desc`, `byDateNewest/Oldest`), plus parallel
  `strategyLabels`. The **shared signature is what makes them swappable**.
- **`sortable-list.js`** — `<sortable-list strategy="…">`. Owns **no sort logic**: it looks
  up `strategies[this.strategy] ?? strategies.byNameAsc` and calls `Array.sort`. Changing
  the `strategy` attribute (via a delegated `<select>` change handler) re-renders.
- **`main.js`** — sets `list.items` to a sample fruit array.

---

## 08 — Dependency Injection  (`Batch-2-of-patterns`)

**Idea:** give a component the dependency it needs (a storage *interface*) rather than
hardcoding a concrete one.

- **`storage.js`** — `MemoryStorage` and `LocalStorageAdapter`, both implementing the same
  interface (`save`/`load`/`getName`). A `StorageFactory.createStorage(type, options = {})`
  picks one. The unused `options` param is intentional — "elastic / trap-door" signature so
  future backends can take config **without breaking callers**.
- **`task-list.js`** — a `<task-list storage="…">` web component that gets its storage from
  the factory in `connectedCallback`. Uses `Promise.resolve(...)` around save/load so the
  **same code works for sync or async** backends; on a failed save it **reverts** (e.g.
  `tasks.pop()`).
  - **⚠️ Planted bug (used in Variant 4):** `render()` ends by calling `setupListeners()`,
    which calls `this.addEventListener('click', …)` — so **a new listener is added on every
    render** and they accumulate. The component's own note admits it's not ideal; the fix is
    to attach the delegated listener **once** in the constructor.
- **`main.js`** — just imports the component.

---

## 09 — Sortable-Storable List Capstone  (`Batch-2-of-patterns`)

**Combines** Strategy (07) + DI (08): two **independent axes**.

- **`sortable-storable-list.js`** — `observedAttributes = ['strategy','storage']`. Changing
  `strategy` only re-renders (re-sorts); changing `storage` calls `swapStorage` →
  `StorageFactory.createStorage` and shows **that backend's own items** (switching
  **does not migrate** items — a documented design choice). `attributeChangedCallback`
  guards `!this.isConnected || oldValue === newValue`. Emits `item-added`/`item-removed`
  events. All listeners delegated in the constructor.
  - **Point:** sorting and storage share no code path → orthogonality / SRP; either can
    change without touching the other.
- Reuses `strategies.js` and `storage.js` from 07/08.

---

## 10 — State Machine  (`Batch-2-of-patterns`)

**Idea:** make valid states and transitions explicit; reject anything not in the table.

- **`machine.js`** — a `transitions` table (`idle→loading→success|error`, with
  `reset`/`retry`) and `next(state, event)`, which returns the target state or **the same
  state + a console warning** for an invalid transition.
- **`fetch-view.js`** — holds a single `this._state`. `dispatch()` translates UI intents
  (`fetchOk`/`fetchFail`/`retry`/`reset`) into machine events; async side-effects run
  **only if the synchronous transition was accepted** (`if (this._transition('fetch'))…`).
  `_simulateFetch` guards `if (this._state !== 'loading') return` before applying results.
  - **Gotchas noted in comments:** prevents "boolean soup" (one valid state at a time); a
    `setTimeout` could fire after the element is removed (detached shadow root) — the fix
    belongs to **Lesson 11** (track/clear it in `disconnectedCallback`); full re-render
    loses focus.
- **`main.js`** — imports + uses the component.

---

## 11 — Robustness  (`Batch-2-of-patterns`)

**Idea:** real systems fail — handle timeouts, retries, typed errors, and teardown safely.

- **`user-card.js`** — `<user-card scenario="active|suspended|delayed:N|error|timeout">`.
  Explicit states `idle→loading→success|error`. Key pieces:
  - `fetchWithTimeout(url, ms)` — an **`AbortController`** + `setTimeout(abort)` (cancels
    the request, unlike `Promise.race` which leaves it running); `clearTimeout` in
    `finally`; **checks `res.ok`** because `fetch` only rejects on **network-level** failure
    (an HTTP 500 still resolves).
  - A custom **`TimeoutError`** subclass so `catch` can branch on `instanceof` instead of
    string-matching.
  - An **`isConnected` guard** before any post-`await` DOM write (the element may have been
    removed while the promise was pending).
  - Constants `TIMEOUT_MS`/`RETRY_ATTEMPTS` (no magic numbers); a single `catch` handles
    typed, generic, and fetch-level errors.
  - A comment flags that depending on a public endpoint (httpbin) is itself fragile — the
    real fix is **DI of `fetch`** (callback to lesson 08).
- **`test-bench.js`** — buttons that **deliberately misbehave**: one `throw`s synchronously,
  one does `Promise.reject(...)` with no `.catch()`. "Run" spawns user-cards for every
  scenario.
- **`main.js`** — registers **last-resort global handlers**:
  `window.addEventListener('error', …)` (uncaught sync throws) and `'unhandledrejection'`
  (promise rejections nothing caught). Both paint a banner and explain that production would
  forward to Sentry/Rollbar/Datadog — the **last line of defense**, reached only when no
  local `try/catch`/`.catch()` handled it.

---

## 12 — Stateful Form Capstone  (`Batch-2-of-patterns`)

**Combines** state machine (10) + robust async (11) + accessibility.

- **`submit-form.js`** — states `idle→submitting→success|error` (with `retry`/`reset`).
  Notable details:
  - The shadow DOM is built **once** with two stable regions: a `.form-container` that's
    fully re-rendered, and a **persistent `.aria-live` region** whose only its `textContent`
    is updated. **Recreating a live region per render makes screen readers miss
    announcements** — so it stays put.
  - The submit handler is synchronous and calls `this._submit()` **without `await`**;
    `_submit` owns its **own `try/catch`**, so a failure can't escape as an **unhandled
    rejection**. Errors set `this._error` and transition to `error`.
  - Async work is gated on `if (this._transition('submit'))`; a defensive
    `if (this._state !== 'submitting') return` after the await ignores stale results.
  - Entering `idle` clears stale message/error.
- **`machine.js`** — the form's transition table; **`main.js`** registers the component.

---

## 13 — CRUD  (`Batch-2-of-patterns`)

**Idea:** a service **facade** over a swappable backend; the UI never touches the backend
directly.

- **`memory-backend.js`** — `MemoryBackend` with private `#tasks`/`#nextId` and
  `list/create/update/remove`, **all `async`** (with a simulated `#delay`) so the same
  interface works for memory **or** REST. `update` **throws** `Task ${id} not found` rather
  than failing silently.
- **`tasks-service.js`** — `TasksService` is a thin facade: its `list/create/update/remove`
  just call the backend. The component depends on **this stable interface**, so the backend
  can be swapped without touching the UI.
- **`task-manager.js`** — the component. Tracks `_listState`, `_error`, `_opError`,
  `_pendingIds`, `_isCreating`. Implements **optimistic updates**: apply the change locally
  → `await service.update` → **reconcile** `this._tasks` with the returned `serverTask`
  (a REST backend may add `updatedAt`/normalized fields the memory one doesn't) → **revert**
  to `original` + show an error toast on failure. `_pendingIds` (a `Set`) blocks concurrent
  ops on the same id. Listeners are delegated in the constructor (submit/change/click).
  Full re-render is acknowledged to lose focus.
- **`main.js`** — `new TasksService(new MemoryBackend())`, inject into the component.

---

## 14 — Routing  (`Batch-2-of-patterns`)

**Idea:** the **URL hash is the source of truth** for which view is shown.

- **`router.js`** — `register(path, elementName, title)` fills a `Map`; `start()` binds
  `hashchange` (guarded against double-start) and renders; `currentPath()` reads
  `location.hash` (falling back to the default); `render()` `replaceChildren`s the outlet
  with a fresh view element (or a "No route" message) and sets `aria-current` on the active
  nav link. **Back/forward buttons work for free** because they just change the hash.
  - **Documented limitations:** no route params, no query strings, no nested routes, no
    programmatic `navigate()`. The router now **owns the hash**, so in-page `#anchor` links
    **collide** with route matching.
- **`list-view.js` / `preferences-view.js`** — **light DOM on purpose** (no shadow root) so
  the page's **global stylesheet** reaches them; a comment explains shadow DOM is right for
  *widgets*, light DOM for *page-level views*. Because the router **destroys and recreates**
  a view on each navigation, the preferences view stores its theme in **`localStorage`**
  (state that must outlive the component lives outside it).
- **`main.js`** — builds the `Router`, registers `/list` and `/prefs`, `start()`. (The saved
  theme is applied by an inline `<head>` script **before** this module runs, to avoid
  **FOUC** — a flash of the wrong theme before JS loads.)

---

## The big mental model

For every example, the question is the same:

> **What can change without forcing everything else to change?**

- Pub/Sub & delegation: add publishers/children without touching the listener.
- Components: change styles/text/logic in isolation; communicate by events.
- MVC: change Model logic without rewriting the View.
- Strategy: add a sort mode without editing the component.
- DI: swap memory ↔ localStorage ↔ REST without touching the UI.
- State machine: change the async flow by editing legal transitions.
- CRUD facade: change the backend without changing the component.
- Routing: change the active page by changing the URL.

Patterns aren't code shapes for their own sake — they're ways to **control dependency,
change, and complexity**, and each one trades something away (more files, more indirection,
focus loss on full re-render, cleanup obligations). Knowing the trade-off is the point.
