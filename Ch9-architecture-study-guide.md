# 09 - Architecture Study Guide

Focus: bold/highlighted ideas, differently styled terms, and the example-question section.

## Core Thesis

Software architecture is about the important decisions that shape a system, especially decisions that are hard or costly to change later.

Know this definition:

- Architecture covers the **important stuff**.
- Architecture covers the **hard to change** stuff.
- Grady Booch framing: architecture is the significant design decisions that shape a system, where significance is measured by cost of change.
- Greg Wilson framing: architecture includes structure, behavior, design, tradeoffs, and effects on maintainability, scalability, etc.

Practical takeaway: architectural work happens whether you plan it or not. If you do it thoughtlessly, you can still produce an architecture, just often a bad or accidental one.

## Architecture Decisions And Documentation

Because architecture decisions are long lasting, document them.

- Use Architectural Decision Records (ADRs) to capture important decisions.
- Diagrams are also architectural artifacts.
- Keep diagrams at the right level of detail, from high-level context to lower-level structure.
- For the course, Markdown or MADR-style ADRs are recommended.

## The “Ilities”

The “ilities” are project qualities that should drive architectural decisions.

Memorize these examples:

- **Utility**: the system provides required functions.
- **Availability**: users can access the system and its functions.
- **Performance**: users can access functions within acceptable time.
- **Accessibility**: users are able to access/use functions.
- **Usability**: users can use functions successfully.
- **Satisfaction**: users enjoy the functions.

Also watch for availability, maintainability, usability, security, performance, and reliability as common architectural concerns.

## Tradeoffs

Architecture is not about finding a perfect answer. It is about balancing tradeoffs.

High-priority bold ideas:

- **It depends**: real architecture decisions depend on context.
- **Tech debt**: short-term decisions can add future cost.
- **Tech investment**: work done now may pay off later.
- **Acknowledge the risk of debt and the wisdom of investment.**
- **A perfect decision now might not be perfect later.**
- **Premature optimization, premature abstraction, and premature automation** are dangers.

Use the “Lambo problem” idea: do not solve luxury/future-scale problems before they are real.

## Common Catchphrases

Know these and their limits:

- **Form Follows Function**: design should follow what the system must do.
- **YAGNI**: do not add features, tools, or abstractions before you need them.
- **KISS**: keep complexity down.
- **DRY**: avoid duplication; keep one source of truth.
- **Embrace Trade-offs**: you usually cannot maximize everything at once.

Important example quote: DRY reduces duplication; orthogonality reduces interdependency among system components.

## Abstraction

Abstraction should hide details so code can change without breaking unrelated parts.

Key examples:

- **Good data hiding** exposes safe, controlled ways to interact with important data.
- **Poor data hiding** lets outside code directly access or change internals.
- `BankAccount`, `deposit()`, `withdraw()`, and `showBalance()` illustrate controlled access.
- **Appropriate abstraction should let you modify code without affecting unrelated code.**

Dependency Injection and IoC:

- **Dependency Injection**: a component receives dependencies from outside instead of creating them itself.
- **Inversion of Control (IoC)**: control flow is flipped; framework or higher-level code calls your code.
- **With IoC**, you write logic but something else controls when/how it runs.
- **Without IoC**, your code controls everything directly.
- Be cautious: IoC/frameworks can become loss of control if you do not understand behavior.

Abstraction warning:

- Over-generalization can create bloat.
- Do not handle every edge case just because it might exist.
- Ask what is actually needed.

## Decomposition, Cohesion, Coupling, Communication

Decomposition:

- Break systems into appropriately sized parts.
- Too large and too small are both problems.
- **Single Responsibility Principle (SRP)**: parts should have focused responsibility.
- Microservices are networked decomposition; monoliths are larger unified structures.
- Microservices can add orchestration, communication overhead, and network failure risk.
- Layered architecture and MVC are common decomposition patterns.

Cohesion:

- **High cohesion** means grouped things belong together and serve a common purpose.
- **Low cohesion** is a grab bag of unrelated things.

Coupling:

- Loose coupling is generally preferred because parts are easier to reason about and swap.
- Over-decomposition can increase coupling and communication overhead.

Communication:

- Decomposed parts still need to communicate.
- Network communication adds overhead and failure risk.
- Communication can be synchronous or asynchronous.
- Event-driven thinking is important for networked systems.

## Patterns

Highlighted warning:

- **Early Pattern Warning**: patterns can help, but they can also become a crutch.

Patterns can be useful because they:

- create shared language,
- provide reusable solution shapes,
- help professionals communicate,
- work well with repeated AI/code generation patterns.

Patterns can be harmful because they:

- create exclusionary jargon,
- encourage force-fitting problems to familiar patterns,
- differ across languages/domains,
- can cause AI or developers to over-apply familiar solutions.

Examples to recognize:

- **Client-Server**
- **Local First / Offline First**
- **Layered**
- **CRUD**
- **MVC**
- **PubSub**
- **Entity component system (ECS)**
- OOP pattern groups: **Creational**, **Structural**, **Behavioral**

Important warning: be extremely cautious with implicit OOP pattern bias. Some problems do not model well as objects, and declarative tools like JSON DSLs, SQL, HTML, and CSS can be appropriate without OOP thinking.

## Architecture Over Time

Architecture must support change, but no architecture is permanent.

- Systems decay, requirements change, and good decisions can become bad later.
- Design for flexibility, modifiability, and maintenance.
- Do not assume change can always be addressed perfectly.
- Capture choices so future developers understand why decisions were made.

## Example Questions To Practice

1. What does “architecture represents significant design decisions” mean?
   - Answer should mention decisions, design, system shape, significance, and cost of change.

2. Why are “all models wrong, but some are useful” and “it depends” relevant to architecture?
   - Models simplify reality; architecture models are useful when they guide decisions, not when they are treated as universal truth.

3. Explain DRY vs orthogonality.
   - DRY minimizes duplication.
   - Orthogonality reduces interdependence among components.

4. Give an example of tech debt and tech investment.
   - Debt: rushing a design that must be fixed later.
   - Investment: research, refactoring, cleaning code, or documenting architecture before it pays off immediately.

5. Why can premature optimization, abstraction, or automation be harmful?
   - They solve future or imaginary problems, add complexity, and may make the current system harder to understand/change.

6. Compare procedural, object-oriented, functional, and declarative paradigms in architecture.
   - Procedural: step-by-step operations.
   - Object-oriented: objects/classes encapsulate state and behavior.
   - Functional: functions and transformations are central.
   - Declarative: describe desired result, not exact control flow.
   - Important quote idea: things hard for OO may be easy for procedures, and vice versa.

7. What are ADRs and why do they matter?
   - ADRs capture important architectural decisions for future access and understanding.

8. Why can microservices be both good and bad?
   - Good: focused responsibility and independent pieces.
   - Bad: network overhead, orchestration complexity, failure points, and over-decomposition.

9. What is the relationship between cohesion and coupling?
   - High cohesion means each part is focused.
   - Loose coupling means parts are not overly dependent.
   - Good architecture tries to improve both, but tradeoffs exist.

10. What is the danger of pattern-driven architecture?
    - Patterns may create shared language, but they can also become jargon, competency signaling, or a way to force-fit solutions.

## Quick Memory Hooks

- Architecture = important + hard to change.
- “Ilities” = qualities architecture must support.
- ADR = why we chose this.
- DRY = less duplication.
- Orthogonality = less interdependence.
- KISS/YAGNI = avoid unnecessary complexity.
- High cohesion, loose coupling.
- Microservices trade decomposition for communication cost.
- Patterns are tools, not laws.
