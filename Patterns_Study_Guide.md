# Patterns Study Guide

This guide summarizes the pattern examples in this folder:

- `sample-pattern-code`: lessons 01-06
- `Batch-2-of-patterns`: lessons 07-14

The main goal is not to memorize every line of code. The goal is to understand how each pattern changes the architecture: who knows about whom, what is decoupled, what can change independently, and what code smells the pattern helps avoid.

## Big Idea

A design pattern is a reusable architectural move.

When reading pattern code, ask:

- What problem is this pattern solving?
- Which objects, components, or modules are being separated?
- What can now change without forcing everything else to change?
- What code smell would appear if we did not use this pattern?
- What tradeoff does the pattern introduce?

Most of these examples are about managing dependencies. Good architecture controls how much one part of the program knows about another part.

## Pattern Overview

| Lesson | Pattern | Core Concept |
|---|---|---|
| 01 | Pub/Sub | One component publishes an event; another listens without direct references. |
| 02 | Event Delegation | One parent listener handles many child events through bubbling. |
| 03 | Notifications Hub Capstone | Combines pub/sub and delegation: child publishers emit events, parent hub routes them. |
| 04 | Components and Decomposition | Split component logic, template, styles, and translations into separate modules. |
| 05 | MVC | Model owns data, View emits user intent, Controller connects them. |
| 06 | Todo MVC Capstone | Full example combining components, MVC, delegated events, and model events. |
| 07 | Strategy | Swap behavior by selecting interchangeable functions with the same interface. |
| 08 | Dependency Injection | Component depends on an abstraction, not one hardcoded implementation. |
| 09 | Sortable/Storable Capstone | Combines strategy and dependency injection. |
| 10 | State Machine | Valid states and transitions are explicit in a transition table. |
| 11 | Robustness | Handle async errors, retries, timeouts, and disconnected components safely. |
| 12 | Stateful Form Capstone | Combines state machine and robust async form submission. |
| 13 | CRUD | Service facade over backend operations: create, read, update, delete. |
| 14 | Routing | URL hash controls which view/component is active. |

## 01. Pub/Sub

Pub/Sub means publisher/subscriber.

One component publishes an event. Other components subscribe to that event. The publisher does not directly call the subscriber.

In the code, look for:

- `dispatchEvent(...)`
- `new CustomEvent(...)`
- `addEventListener(...)`
- `event.detail`
- `bubbles: true`
- `composed: true`

Conceptually:

- The publisher says, "Something happened."
- The subscriber says, "I care when that thing happens."
- The publisher does not need to know who is listening.

Why it matters:

- Reduces direct coupling between components.
- Makes components easier to reuse.
- Allows multiple listeners to react to one event.

Code smell it avoids:

- Component A directly calling methods on Component B.
- Components needing hardcoded references to each other.

Example reasoning question:

> Why use a custom event instead of directly calling another component's method?

Answer:

Because direct calls create tight coupling. Events let components communicate through a shared contract without needing to know each other's internal implementation.

## 02. Event Delegation

Event delegation means putting one listener on a parent instead of many listeners on children.

In the code, look for:

- A listener attached to a parent container.
- Events bubbling up from child elements.
- `event.target`
- `event.target.closest(...)`
- `dataset.action` or `dataset.role`

Conceptually:

- Children emit events.
- The parent catches them.
- The parent decides what to do based on the event target or event data.

Why it matters:

- Scales better when there are many child elements.
- Works with dynamically added children.
- Reduces repeated event listener setup.

Code smell it avoids:

- Adding a separate click listener to every button or list item.
- Rebinding listeners every time the DOM re-renders.

Example reasoning question:

> Why does event delegation still work when new child elements are added later?

Answer:

Because the listener is on the parent, not the individual children. As long as the new child event bubbles to the parent, the parent can handle it.

## 03. Notifications Hub Capstone

This combines pub/sub and event delegation.

The child status publishers emit `notify` events. The notifications hub catches those events with one delegated listener and routes notifications into per-source stacks.

Key concept:

- Children publish messages.
- Parent hub delegates handling.
- Parent reads event data instead of inspecting child internals.

Why it matters:

- Adding another publisher does not require changing the hub.
- The hub does not need to know how each publisher is implemented.

Code smell it avoids:

- Parent querying child internals.
- Manually wiring every child to the parent.

## 04. Components and Decomposition

Decomposition means splitting a large component into smaller files or responsibilities.

In this folder, components often separate:

- Main component logic: `.js`
- HTML rendering: `-template.js`
- CSS: `-styles.js`
- Translations: `-i18n.js`

Conceptually:

- Logic decides what happens.
- Template decides what markup is rendered.
- Styles decide how it looks.
- i18n decides what text appears for each language.

Why it matters:

- Easier to read.
- Easier to test.
- Easier to change one concern without affecting others.

Code smell it avoids:

- Huge components with logic, HTML, CSS, translation text, and event handling all mixed together.

Tradeoff:

- More files to navigate.
- Over-decomposition can make simple code harder to follow.

## 05. MVC

MVC means Model, View, Controller.

In these examples:

- Model owns data and business logic.
- View displays UI and emits user intent.
- Controller connects the model and the view.

The model usually extends `EventTarget`, so it can emit change events.

In the code, look for:

- `model.js`
- `controller.js`
- View components that dispatch `intent` events.
- Model events like `value-changed` or `todos-changed`.

Conceptually:

- View says, "The user wants to increment."
- Controller says, "That means call `model.increment()`."
- Model updates state and emits an event.
- Controller updates the view.

Why it matters:

- Model does not know about the DOM.
- View does not know how data is stored.
- Controller is the bridge.

Code smell it avoids:

- UI elements directly mutating application state everywhere.
- Business logic inside DOM event handlers.
- Model code depending on HTML structure.

Example reasoning question:

> Why should the model not directly update the DOM?

Answer:

Because the model should represent application state and rules, not presentation. Keeping it DOM-free makes it easier to test and reuse.

## 06. Todo MVC Capstone

This combines:

- Web components.
- MVC.
- Pub/sub-style model events.
- View `intent` events.
- Event delegation inside components.

The `TodoModel` owns the todo array. The input and list components emit user intent. The controller translates intent into model calls.

Key architectural contract:

- Model does not know views.
- Views do not know model.
- Controller knows both.

This is a very important exam-style example because it shows multiple patterns working together.

## 07. Strategy

Strategy means interchangeable behavior.

In the sorting example, each strategy is a comparator function:

```js
(a, b) => number
```

Examples:

- Sort by name A-Z.
- Sort by name Z-A.
- Sort by newest date.
- Sort by oldest date.

The list component does not know how each strategy works. It only knows that each strategy has the same interface.

In the code, look for:

- A `strategies` object.
- Multiple functions with the same signature.
- Runtime selection of one behavior.

Why it matters:

- Add new behavior without rewriting the component.
- Avoid large conditional chains.

Code smell it avoids:

- Giant `if/else` or `switch` statements for every sorting mode.
- Hardcoding behavior inside a component.

Example reasoning question:

> Why is the shared function signature important?

Answer:

Because the caller can use any strategy the same way. This makes the strategies interchangeable.

## 08. Dependency Injection

Dependency injection means giving a component the dependency it needs instead of hardcoding that dependency inside the component.

In the storage example, the task list can use:

- Memory storage.
- LocalStorage.
- Potentially another storage backend later.

The component talks to a storage interface:

- `save(...)`
- `load(...)`
- `getName(...)`

Conceptually:

- Bad: "I always use localStorage."
- Better: "Give me something that behaves like storage."

Why it matters:

- Easier to test.
- Easier to swap implementations.
- Reduces hardcoded dependencies.

Code smell it avoids:

- Component directly using `localStorage` everywhere.
- Component depending on a concrete backend.

Tradeoff:

- More setup code.
- More abstraction.

## 09. Sortable/Storable List Capstone

This combines:

- Strategy for sorting.
- Dependency injection for storage.

The component has two independent choices:

- `strategy`: how items are sorted.
- `storage`: where items are stored.

Changing sort strategy should not require changing storage logic.

Changing storage should not require changing sort logic.

That independence is the architectural point.

## 10. State Machine

A state machine makes states and valid transitions explicit.

Example states:

- `idle`
- `loading`
- `success`
- `error`

Example events:

- `fetch`
- `resolve`
- `reject`
- `reset`
- `retry`

In the code, look for a transition table like:

```js
export const transitions = {
  idle:    { fetch: 'loading' },
  loading: { resolve: 'success', reject: 'error' },
  success: { reset: 'idle' },
  error:   { reset: 'idle', retry: 'loading' },
};
```

Conceptually:

- The current state controls what events are valid.
- Invalid transitions are rejected.
- Impossible states become harder to create.

Why it matters:

- Prevents confusing UI states.
- Makes async flows easier to reason about.
- Gives you a clear contract for behavior.

Code smell it avoids:

- Many booleans like `isLoading`, `hasError`, `isSuccess`, `isRetrying`.
- Contradictory states, like loading and success at the same time.

Example reasoning question:

> Why is a state machine better than several boolean flags?

Answer:

Because boolean flags can combine into invalid states. A state machine allows only one valid state at a time and defines legal transitions.

## 11. Robustness

Robustness means code handles failure safely.

The robustness example demonstrates:

- `try/catch`
- Typed errors like `TimeoutError`
- `AbortController`
- Retry behavior
- Timeout handling
- Guarding against updates after a component is disconnected

Important concept:

`fetch` only rejects for network-level failures. A server response like HTTP 500 still resolves, so code must check `res.ok`.

Why it matters:

- Real systems fail.
- Requests may time out.
- Components may be removed before async work finishes.
- Users need meaningful error messages.

Code smell it avoids:

- Unhandled promise rejections.
- Generic "something went wrong" messages.
- Updating DOM after a component is no longer connected.
- Letting slow requests run forever.

Example reasoning question:

> Why use `AbortController` instead of only `Promise.race` for timeouts?

Answer:

`Promise.race` may ignore a slow request, but the request can keep running in the background. `AbortController` actually cancels the fetch.

## 12. Stateful Form Capstone

This combines:

- State machine.
- Robust async form submission.
- Retry/cancel behavior.
- Accessible status updates.

The form moves through:

```text
idle -> submitting -> success
                  -> error
```

Important details:

- The submit action transitions to `submitting`.
- Success transitions to `success`.
- Failure transitions to `error`.
- Retry returns to `submitting`.
- Cancel/reset returns to `idle`.

The code also keeps a persistent `aria-live` region. This matters because recreating the live region on every render can make screen readers miss announcements.

Code smell it avoids:

- Form states scattered across many conditionals.
- Async errors escaping event handlers.
- Users getting stuck after failure.

## 13. CRUD

CRUD means:

- Create
- Read
- Update
- Delete

The task manager talks to a service with methods like:

- `list()`
- `create(...)`
- `update(...)`
- `remove(...)`

The service wraps a backend. The backend might be memory storage today and REST later.

Conceptually:

- Component talks to service.
- Service talks to backend.
- Backend details are hidden from the component.

Why it matters:

- UI does not change when backend changes.
- Backend logic is isolated.
- Operations have clear names and responsibilities.

Important CRUD UI ideas:

- Loading state for reads.
- Operation errors for create/update/delete.
- Optimistic updates for faster UI.
- Revert on failure.
- Reconcile local UI with server response.

Code smell it avoids:

- Fetch calls scattered directly throughout UI components.
- UI tightly coupled to one backend.
- No clear place for create/read/update/delete rules.

Example reasoning question:

> Why use a service facade instead of calling the backend directly from the component?

Answer:

The service hides backend details and gives the component a stable interface. The backend can change without forcing the component to change.

## 14. Routing

Routing maps a URL to a view.

In this example, the hash part of the URL controls the active view:

```text
#/list
#/prefs
```

In the code, look for:

- `window.location.hash`
- `hashchange`
- A route table or map.
- An outlet element.
- Replacing the active view.

Conceptually:

- The URL is the source of truth for the current page/view.
- Changing the URL changes the active component.
- Back and forward browser buttons work naturally with hash changes.

Why it matters:

- Makes navigation shareable/bookmarkable.
- Separates views.
- Gives the app a navigation structure.

Code smell it avoids:

- Hidden app navigation state that is not reflected in the URL.
- Manually showing/hiding many sections with scattered conditionals.

Tradeoff:

- The router now owns the hash.
- In-page anchor links can conflict with hash routing.

## Code Smells and Matching Patterns

| Code Smell | Pattern That Helps |
|---|---|
| Components directly calling each other | Pub/Sub |
| Listener attached to every child | Event Delegation |
| One huge component file | Components and Decomposition |
| UI and data logic mixed together | MVC |
| Giant conditional for interchangeable behavior | Strategy |
| Hardcoded backend/storage/API | Dependency Injection |
| Conflicting boolean state flags | State Machine |
| Unhandled async failure | Robustness |
| Fetch calls scattered across UI | CRUD with Service Facade |
| Navigation state hidden from URL | Routing |

## How to Analyze Any Example

Use this checklist when reading code:

1. Identify the moving parts.
   - Components?
   - Model?
   - Controller?
   - Service?
   - Backend?
   - Router?

2. Identify the communication style.
   - Direct method call?
   - Custom event?
   - Model event?
   - Service method?
   - URL hash?

3. Identify the dependency direction.
   - Who knows about whom?
   - Which module is isolated?
   - Which module acts as the bridge?

4. Identify what can change independently.
   - Can sorting change?
   - Can storage change?
   - Can the backend change?
   - Can the UI change?
   - Can the states change?

5. Identify the code smell avoided.
   - Tight coupling?
   - Huge component?
   - Boolean chaos?
   - Repeated listeners?
   - Hardcoded dependencies?

## High-Value Exam Reasoning Prompts

### Pub/Sub

Question:

> Why use custom events between components?

Short answer:

To decouple components. The publisher does not need a direct reference to the subscriber.

### Event Delegation

Question:

> Why attach one listener to the parent?

Short answer:

It scales better and works for dynamically added children because events bubble up.

### MVC

Question:

> Why should the model not know about the view?

Short answer:

The model should represent data and rules. Keeping it separate from the DOM makes it reusable and testable.

### Strategy

Question:

> What makes strategy functions interchangeable?

Short answer:

They all share the same interface. The caller can use any of them without knowing their internal logic.

### Dependency Injection

Question:

> Why inject storage instead of hardcoding localStorage?

Short answer:

So the component can work with different storage implementations and becomes easier to test.

### State Machine

Question:

> What problem does a state machine solve?

Short answer:

It prevents invalid combinations of state and makes valid transitions explicit.

### Robustness

Question:

> Why check `res.ok` after fetch?

Short answer:

Because HTTP error responses like 500 still resolve as fetch responses. They do not automatically throw.

### CRUD

Question:

> Why use a service layer?

Short answer:

It gives the component a stable interface and hides backend implementation details.

### Routing

Question:

> Why make the URL the source of truth?

Short answer:

It makes navigation shareable, bookmarkable, and compatible with browser back/forward behavior.

## The Main Mental Model

For every pattern, ask:

> What can change without forcing everything else to change?

Examples:

- Strategy: sorting behavior can change without changing the list component.
- Dependency injection: storage backend can change without changing the UI.
- MVC: model logic can change without rewriting the view.
- Routing: active page can change by changing the URL.
- State machine: async flow can be controlled through legal transitions.
- CRUD service: backend implementation can change without changing the component.

Patterns are not just code shapes. They are ways to control dependency, change, and complexity.

