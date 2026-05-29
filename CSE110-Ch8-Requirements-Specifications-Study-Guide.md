# CSE 110 Chapter 8 Study Guide: Requirements and Specifications

## Big Picture

Requirements answer: what exactly should the software do?

Specifications answer: how might the team roughly organize the work and approach the solution?

Architecture comes later and gets more precise about system/code-level how.

The main warning of this chapter: if you skip requirements, you do not remove requirements. You only leave them hidden in someone's head until your software fails to meet them.

## Most Testable Ideas

1. Planning is cheaper than coding.
   - A sketch, wireframe, diagram, user story, or algorithm outline is easier to revise than code.
   - People resist planning because coding feels more productive, but unclear planning creates later risk.

2. Requirements need 5W1H thinking.
   - Who is using it?
   - What do they need?
   - When and where do they use it?
   - Why does it matter?
   - How should the system support the task?
   - Warning: focusing only on how makes you solution-focused instead of user-focused.

3. Requirements from people are hard.
   - Stakeholders often speak vaguely.
   - Software needs precision.
   - A software engineer should translate vague human needs into clear, testable requirements.
   - This is like prompting an LLM: vague input can produce surprising or wrong output.

4. The "-ilities" are central.
   - These are mostly non-functional requirements: quality attributes that affect long-term success.
   - The exam will likely ask you to identify which ility applies to a design choice, code snippet, HTML/CSS/JS decision, or requirement.

5. Vague requirements should become concrete.
   - Weak: "The app should be fast."
   - Stronger: "The dashboard should load in under 1 second for 90% of requests on the supported devices and networks."
   - Weak: "The app should be secure."
   - Stronger: "No high or critical vulnerabilities are reported by tool X before release."

6. Requirements documentation must match the project's risk and culture.
   - A startup may use lightweight docs.
   - A defense or safety-critical project may need formal templates.
   - The key is not one perfect style. The key is appropriate formality used consistently.

7. Keep a single source of truth.
   - Plans, diagrams, user stories, notes, tests, and docs should live in the repo or repo wiki, or be linked from there.
   - If knowledge lives only in people's heads or scattered tools, the project becomes fragile.

8. Focus on users over features.
   - Developers think in code, data, and features.
   - Users think in goals, needs, and jobs to be done.
   - User stories connect these: "As a user, I want X so that I can accomplish Y."

9. System model and user model should align.
   - System model: how developers organize the code/data/features internally.
   - User model: how users understand their task and goal.
   - Good software hides unnecessary system complexity and supports the user's mental model.

10. Organizational incentives affect requirements.
   - If teams are rewarded for shipping features but not reliability, they will tend to ship features at the cost of reliability.
   - Requirements are not just technical. They are shaped by incentives, politics, deadlines, and culture.

## All 17 "-ilities"

Know these well enough to recognize them in examples:

1. Usability: how easy and intuitive the software is.
2. Maintainability: how easy it is to modify, fix, or improve.
3. Scalability: how well it handles growth.
4. Availability: how often the system is up and usable.
5. Reliability: how consistently it performs required functions under stated conditions.
6. Portability: how easily it moves to another environment.
7. Testability: how effectively it can be tested.
8. Flexibility: how easily it adapts to changes.
9. Interoperability: how well it works with other systems.
10. Reusability: how much components can be reused elsewhere.
11. Security: protection from unauthorized access or modification.
12. Performance: speed, response time, throughput, and resource behavior.
13. Stability: ability to run without unexpected crashes or behavior.
14. Accessibility: usability by people with different abilities, devices, or constraints.
15. Compatibility: ability to coexist with other systems without degrading behavior.
16. Configurability: ability to tailor behavior without changing source code.
17. Modularity: separation into parts so one change has limited impact.

## The 8 Key ISO 25010 Categories

This is the shorter list the chapter says matters most for class:

| ISO category | What it means | Practical ways to verify |
| --- | --- | --- |
| Functional suitability | Does it do the right things correctly and completely? | Requirements review, acceptance criteria, unit tests, end-to-end tests, TDD |
| Performance efficiency | Does it use time/resources well? | Performance tests, Lighthouse, RUM metrics, load timing budgets |
| Compatibility | Does it coexist/interoperate with other systems? | Browser/device compatibility tests, integration tests |
| Usability | Can real users learn and operate it effectively? | User acceptance testing, usability tests, analytics, accessibility checks |
| Reliability | Does it keep working under expected conditions/failures? | Load tests, fault-injection tests, retries, error handling, uptime checks |
| Security | Does it protect data/actions from misuse? | Security scanning, input validation, auth checks, dependency scanning, CSP |
| Maintainability | Can developers understand, test, modify, and extend it? | Static analysis, modular design, standards, code review, test coverage |
| Portability | Can it move across devices/platforms/environments? | Device testing, containerization, abstraction layers, install tests |

## The Emphasized Quality-Model Image

The image maps the 8 ISO categories to concrete engineering techniques. Study it as "quality attribute -> code/SE technique -> verification."

Functional suitability:
   - Subideas: completeness, correctness, appropriateness.
   - Techniques: complete requirements/specs, TDD.
   - Verification: unit tests, end-to-end tests, CI/CD, Jest, Playwright.

Performance efficiency:
   - Subideas: time behavior, resource use, capacity.
   - Techniques: code minimalism, optimization.
   - Verification: performance testing, Lighthouse, real-user monitoring.

Compatibility:
   - Subideas: coexistence, interoperability.
   - Techniques: abstractions.
   - Verification: browser compatibility tests, BrowserStack-style testing.

Usability:
   - Subideas: learnability, operability, user-error protection, interface aesthetics, accessibility.
   - Techniques: progressive enhancement.
   - Verification: user acceptance testing, analytics/session review.

Reliability:
   - Subideas: maturity, availability, fault tolerance, recoverability.
   - Techniques: type checking, assertions, try/catch.
   - Verification: load testing, chaos/failure testing.

Security:
   - Subideas: confidentiality, integrity, nonrepudiation, accountability, authenticity.
   - Techniques: type checking, assertions, try/catch.
   - Verification: security scanner, dependency scan, auth tests.

Maintainability:
   - Subideas: modularity, reusability, analyzability, modifiability, testability.
   - Techniques: standards and patterns.
   - Verification: static analysis, code review, maintainability metrics.

Portability:
   - Subideas: adaptability, installability, replaceability.
   - Techniques: abstractions, virtualization, cross-platform frameworks.
   - Verification: device tests, install tests, environment tests.

## HTML/CSS/JS Example Pattern

Example from the chapter:

```html
<link rel="stylesheet" href="http://example.org/themes/cool.css">
```

Likely ilities involved:

- Reliability/availability: if the remote host is down, the page may render badly.
- Performance: external CSS can block rendering and slow page load.
- Security: plain HTTP can be modified in transit; third-party CSS can create privacy/security risk.
- Maintainability: a remote stylesheet can change outside your repo and break your design.
- Compatibility: unsupported CSS may behave differently across browsers.
- Usability/accessibility: broken styles may make text unreadable or controls hard to use.

Better design choices:

- Use HTTPS.
- Host or pin critical CSS where possible.
- Add fallback/base styles.
- Use Subresource Integrity when loading third-party assets.
- Test with slow/offline network conditions.
- Keep external dependency decisions documented in the repo.

Other common HTML/CSS/JS examples:

- `<img>`: accessibility needs `alt`; performance needs dimensions/lazy loading; reliability needs fallback behavior.
- `<script>`: security and reliability risk with third-party JS; performance risk if render-blocking.
- Forms: usability and accessibility need labels/errors; security needs validation and sanitization; reliability needs graceful error handling.
- CSS-only visual cues: accessibility risk if meaning depends only on color.
- Dynamic JS UI: reliability/usability risk if the page fails without JavaScript; progressive enhancement can help.

## Rewriting Vague Requirements

Exam move: identify why a requirement is vague, then rewrite it with measurable acceptance criteria.

Weak: "The site should be modern."

Better:
"The site should use the approved design system, pass WCAG AA contrast checks, and receive approval from 3 of 4 test users on visual clarity during usability review."

Weak: "The app should be reliable."

Better:
"The API should return successful responses for 99.9% of requests per month, retry transient failures up to 2 times, and show a user-facing recovery message when the backend is unavailable."

Weak: "Search should be good."

Better:
"Search should return relevant matching items within 500 ms for 95% of queries over the expected dataset size, support typo-tolerant matching, and show an empty-state suggestion when no result is found."

Weak: "The app should be easy to use."

Better:
"A first-time user should be able to complete the main task in under 2 minutes with no external instructions during user testing."

## Example Questions: Answer Frameworks

### 1. Know the ilities and apply them to code/design decisions.

How to answer:

1. Name the relevant ilities.
2. Explain the risk or benefit.
3. Give a concrete verification method.
4. Suggest an improvement.

Example:
"A remote HTTP stylesheet affects security because it can be modified in transit, performance because it adds a render-blocking network request, and reliability because the remote host can fail. I would use HTTPS, add integrity checks, keep critical CSS locally, and test page load under slow/offline conditions."

### 2. Convert vague requirements into specific requirements.

How to answer:

1. Point out the missing details: who, what, when, where, why, how much.
2. Add measurable criteria.
3. Add context and acceptance tests.

Template:
"For [user type], when [context], the system shall [behavior] within [measurable constraint], and success is verified by [test/metric/review]."

### 3. Compare vague LLM prompting to vague stakeholder requirements.

Main answer:

Vague stakeholder input can cause software teams to build the wrong thing, just like vague prompts can cause an LLM to produce unexpected output. A human software engineer can do more than generate an answer: interview users, ask follow-up questions, observe workflows, negotiate tradeoffs, build prototypes, define acceptance criteria, and confirm shared understanding.

### 4. Why are architecture diagrams and notation not standard practice?

Good points:

- Formal notations like UML can feel heavy or slow.
- Systems change faster than diagrams.
- Many teams reward shipping more than documentation.
- Developers may not be trained in shared modeling notation.
- Tools may not fit naturally into the repo/review workflow.
- Ad hoc boxes-and-arrows diagrams are easier, even if less precise.

### 5. Why do requirements documents and diagrams get out of date? How do we fight it?

Why they get stale:

- Code changes without doc updates.
- Docs live outside the repo.
- No clear owner.
- Teams are rewarded for features, not maintenance.
- Requirements change but old artifacts are not retired.

How to fight it:

- Keep docs in the repo or repo wiki.
- Link docs to issues, PRs, tests, and decisions.
- Review requirement/doc updates during PR review.
- Use lightweight docs that people will actually maintain.
- Add owners and dates.
- Treat tests and acceptance criteria as living requirements.

### 6. How can user-centered thinking continue into later development?

Concrete techniques:

- User stories and use cases.
- Jobs-to-be-done statements.
- Personas or real user roles.
- Wireframes and clickable prototypes.
- Acceptance criteria written from the user's perspective.
- End-to-end tests that follow user workflows.
- Usability testing before release.
- Traceability from requirement -> issue -> PR -> test.
- User feedback loops after release.

### 7. Explain system model vs user model and identify repo/code smells.

System model:
The developer's internal view: database tables, services, components, classes, APIs, and implementation structure.

User model:
The user's view: tasks, goals, language, workflows, and outcomes.

Signs they are aligned:

- Routes, pages, and labels use user task language.
- Tests describe user flows, not only internal functions.
- README/docs explain user goals before implementation details.
- User stories map clearly to features and acceptance tests.
- UI organization matches how users actually work.

Misalignment smells:

- Navigation mirrors database tables instead of user tasks.
- UI uses internal engineering names users would not know.
- One user goal requires jumping through unrelated screens.
- PRs only mention technical changes, not user outcomes.
- Tests only check implementation details and miss user workflows.
- Documentation is scattered, stale, or separate from the repo.

## Mini Practice Set

1. A team says: "The app should be secure." Rewrite it as a concrete requirement.
2. A page loads five third-party scripts before rendering. Which ilities are affected?
3. A diagram is stored in a random chat thread and not linked in the repo. What risk does this create?
4. A UI menu is organized around database table names. What model mismatch might exist?
5. A startup has no formal requirements docs but keeps user stories, wireframes, and acceptance tests in the repo. Is that necessarily bad? Explain.
6. A company rewards teams only for new features, while reliability work is invisible. What requirement risk does this create?
7. An image button has no alt text and no keyboard-accessible label. Which ilities are affected?
8. A project has strict UML diagrams but no one updates them after code changes. What is the problem?

## Quick Memory Hooks

- Requirements: what the system must do.
- Specifications: rough organized approach for how to build.
- Architecture: more precise system/code-level how.
- Hidden requirements still exist.
- Vague stakeholder input causes vague or wrong software.
- The 8 key ilities: functional suitability, performance efficiency, compatibility, usability, reliability, security, maintainability, portability.
- Make vague requirements measurable.
- Keep requirements and diagrams near the repo.
- User model should dominate system model.
- Incentives can silently become requirements.
