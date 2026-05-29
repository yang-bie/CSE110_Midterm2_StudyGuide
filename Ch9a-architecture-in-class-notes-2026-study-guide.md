# 09a - In Class Notes - 2026 Study Guide

Focus: highlighted, bold, colored/styled, and question-oriented material from the unfolded Craft page.

## Main Point

The lecture is about weighing engineering design choices, explaining trade-offs, and practicing critical thinking from architecture down to execution.

High-priority exam idea:

- Applying critical reasoning to systems, architecture, and code is the main thing humans still have over code generation systems.
- Do not prefer sounding technical over actually understanding something.
- If you rely on vibes, jargon, or generated code without understanding, you take on career debt or understanding debt.

## Common Advice Given Recently

UI evaluation:

- Keep early UI rough: no fancy pixels first.
- Prefer recognition over recall.
- Aim for less work and more gain.
- Respect conventions: users expect familiar button meanings, cart location, clickable affordances, and common screen layouts.
- Embrace the "99% rule": if almost everyone expects something, breaking it needs a good reason.
- Reject convention only when context justifies it.
- Avoid "death by sameness and regularity."
- Use context of use: device, situation, user goal, and environment matter.
- "Play UI Golf": make the path shorter and simpler.
- When evaluating, be quiet, observe users, and do not guide them to your conclusions.

Careful decisions:

- Architecture, dependencies, frameworks, and tools can be hard to undo.
- Use ADRs and plan carefully for decisions that may become permanent.

Project ownership:

- The styled title says "It's **Your** Project."
- Consult users and stakeholders, but take a stand if you can defend your process.
- Dogfood your product: if you do not love it, users probably will not.

Rough project approach:

- Constraints are advantages because they focus choices.
- Code from the core: start with the core interaction/interface.
- Build fewer features with more focus.
- Love your software.
- Ship early and often.
- Get feedback and iterate, but learn to say no when feedback threatens focus.

## Jargon

Highlighted advice:

- **Advice:** Do not accept the "vibe" of a term without knowing its real meaning.
- Embrace jargon only when you understand it.
- Do not use jargon to confuse others.

Study angle:

- Jargon can be efficient for people who share the meaning.
- Jargon can also exclude, intimidate, or hide weak thinking.
- Avoid "JARGONmonoxide": language that chokes out understanding.

## Foundation vs Fashion

Core idea:

- To grow as a software developer, learn to see the essence of approaches and technologies instead of getting trapped in details.
- If you cannot change your vision, you may miss the underlying sameness between technologies.

Highlighted warning:

- High-level tech layers shift fast; low-level foundations move slowly.
- Tech fashion matters in the short term, especially for career signaling.
- Chasing tech fashion long-term is dangerous.
- Engineering requires balance.

Pace layers:

- Some layers of technology change slowly.
- Others change rapidly.
- Do not confuse fashionable specifics with durable engineering principles.

## Security Model Case Study

The WAF/security example is not mainly about security. It shows that architecture depends on the core model you choose.

Models:

- Negative model / blacklist: allow everything except known bad requests.
- Positive model / whitelist: deny by default; allow only known good requests.
- Behavioral model / ML/AI style: train on activity, then flag or block outliers.

Tradeoffs:

- Negative model: weak security but less likely to hassle normal users.
- Positive model: strong security but requires deep coverage of all legitimate behavior.
- Behavioral model: seems balanced, but training data may miss real usage and create false positives.

Bold takeaway:

- Critical thinking about the essence of what you are doing is key.
- Core principles permeate the whole system.
- If the core model is wrong, you can create "Death Star" level architectural flaws.

## DSL And Data Format Case Study

Formats discussed:

- CSV: `1,true,"UCSD"`
- XML: `<rank>1</rank><in-us>true</in-us><school-name>UCSD</school-name>`
- JSON: `{ "rank" : 1, "in-us" : true, "school-name" : "UCSD"}`
- YAML and binary formats are also possible.

Important bold ideas:

- Do not choose a data format only because it is fashionable.
- Choose based on appropriateness to the task.
- New does not automatically mean better.
- Technical specifics change, but core characteristics often remain stable.
- A Domain Specific Language (**DSL**) can be useful, but give yourself time to do it right and document it strictly.

Tradeoff examples:

- JSON is fashionable now, but not always best.
- XML used to be fashionable.
- YAML was popular in Ruby/Rails contexts.
- Some formats are more human-readable.
- Some are more robust for parsing.
- Some are bulky and affect performance.
- Fixing one issue often creates another tradeoff.

## Charting Case Study

Questions to ask:

- Where should the chart be made: client or server?
- Is interactivity required?
- What is the data size: small, medium, or large?
- What image/rendering format should be used?

Rendering choices:

- Bitmap formats like PNG/JPEG are simple images.
- Canvas is immediate-mode graphics.
- WebGL supports 3D/high-performance graphics.
- SVG is DOM/tag-based, easy to work with, and scales visually, but can have performance problems.

Bold "Sanity Aside" idea:

- Many tradeoff discussions are less about the tech alone and more about how humans actually use the tech.
- Do not buy the "Lambo" because it can go 200mph if your real situation does not need that capability.

## Addressing Tradeoffs

The key word is **appropriate**, not "correct."

Highlighted/bold points:

- **Notice the use of the word appropriate as opposed to correct.**
- **SWE Tip: Solution first thinking should be fought at all times.**
- All tools and tech always have tradeoffs.
- You cannot have everything.
- You may gain performance but increase development time.
- You may reduce development time but lose performance.
- AI-generated code may contain hidden compromises you do not notice.
- Getting too emotionally bound to or against a popular technology is a warning sign.
- Do not ignore observable negative outcomes just because "it is only a tool."
- Accept tradeoffs are inherent with **ALL** tools and tech.
- Use the right tool for the job, but only after understanding the job and the tools.

Question from the notes:

- Do some of these points conflict with AI code approaches? Is that because the professor does not understand AI tools or because he does?

Likely answer direction:

- They conflict with careless AI use, not necessarily with AI itself.
- AI can generate plausible solutions, but engineers still need requirements, context, judgment, and tradeoff awareness.

## Client-Server Model

Client-server is the core model behind many web apps/sites.

Pieces:

- Client: software/system making a request, often a **web browser**.
- Server: software/system servicing a request, often a **web server**.
- Network: public internet or private/internal network.

Fat vs thin client:

- **"Fat client"**: client does most computation.
- **"Thin client"**: server does most work; client mostly displays.

Client-side characteristics:

- Rarely under developer control.
- Possibly hostile.
- Highly variable in technical ability.
- Can provide fast/rich interactions because it avoids network round trips.

Server-side characteristics:

- More under our **control**.
- Can be secured, though hostile requests are still possible.
- Capacity is variable but more predictable because we provision it.
- Major downside: network constrained.

Bold network warning:

- The network is highly variable.
- Developers often wrongly assume network communications are robust and consistent.

Localhost Effect:

- **"The Localhost Effect"** is when local development hides real network problems.
- Localhost makes the web look like a happy path.
- Public production users may have a very different experience.
- Takeaway: build public apps defensively for a wild network.

## CRUD

CRUD = Create, Read, Update, Delete.

The bold letters:

- **C** = Create
- **R** = Read
- **U** = Update
- **D** = Delete

Meaning:

- CRUD covers actions performed on data/resources.
- It is simple technically but difficult socially/contextually.
- The hard part is knowing what you are actually "CRUDing."

Domain Driven Design:

- DDD means carefully mapping the problem domain into code.
- Developers often need domain experts and stakeholders.
- A ubiquitous language helps product, users, domain experts, and developers communicate consistently.

## REST

REST = REpresentational State Transfer.

Core ideas:

- Roy Fielding's PhD work around 2000.
- Resources are represented as URLs.
- HTTP methods are verbs/actions on those resources.
- URLs behave like nouns.
- HTTP methods behave like verbs.
- A URL can be thought of as a command-line-like interface.

Examples:

- `GET /dogs/1 HTTP 1.1`
- `/dogs`
- `POST`: create a new entity.
- `GET`: fetch entities.
- `PUT` or `PATCH`: update an entity.
- `DELETE`: delete an entity.

API design warning:

- APIs are user interfaces for other developers.
- Be careful with endpoint design.
- Avoid painting yourself into a corner like `http://example.com/api/v2`.
- Think about expandability, trap doors, and escape hatches.
- Document APIs with OpenAPI/Swagger.
- Use mocks and API contract thinking before committing to execution.

## MVC

MVC separates application concerns:

- **Model**: manages data, logic, and rules.
- **View**: presents data to users.
- **Controller**: handles input and coordinates model/view.

Web mapping:

- Model -> database/API interactions.
- View -> templates, HTML, CSS, JS.
- Controller -> client-side or server-side request handlers.

Highlighted MVC/fashion-foundation warning:

- Patterns often have many variations.
- Ask whether solution complexity is worth it.
- Be pragmatic: understand the nature of the problem and who you are solving it for.
- Fashion is the short-term market-safe bet.
- Foundation is the better long-term engineering bet.
- If market pressure forces a fashionable solution, document that honestly in an ADR.

## Architectural Patterns: Promise And Problem

Bold/highlighted closing ideas:

- There is value in leveraging architectural patterns.
- Do not reinvent everything.
- But do not rush into doing without understanding.
- **You should not apply a pattern until you deeply understand both the problem and the pattern.**
- Shift thinking left: start with high-level patterns, then work downward.

## Practice Questions

1. Why is "appropriate" a better word than "correct" for architecture decisions?
   - Because engineering choices depend on requirements, constraints, users, context, and tradeoffs.

2. Explain "solution first thinking" and why it is dangerous.
   - It means choosing a tool/solution before understanding the problem. It hides requirements and bakes in bad tradeoffs.

3. How does AI-generated code increase the need for engineering judgment?
   - Generated code may include hidden compromises, pattern bias, or tradeoffs the developer does not understand.

4. Compare negative, positive, and behavioral security models.
   - Negative blocks known bad things.
   - Positive allows only known good things.
   - Behavioral learns normal activity and flags outliers.

5. Why can technology fashion be useful short term but dangerous long term?
   - It may help with career/social adoption, but durable systems need foundational characteristics, not just trendy tools.

6. What is the Localhost Effect?
   - Local development hides real network variability, making apps seem more reliable than they will be in production.

7. What are the tradeoffs between fat and thin clients?
   - Fat clients can be interactive and fast locally but are harder to secure/control. Thin clients centralize control but depend heavily on network/server performance.

8. Why is CRUD "simple technically but hard contextually"?
   - Create/read/update/delete are simple actions, but the domain meaning, user workflow, and real data rules are hard.

9. How does REST map URLs and HTTP methods?
   - URLs represent resources/nouns; HTTP methods represent actions/verbs.

10. Why should APIs be treated as user interfaces?
    - Developers are users of APIs; unclear endpoints, poor versioning, and weak documentation create usability problems.

11. What are Model, View, and Controller?
    - Model manages data/rules, View presents data, Controller handles input/coordination.

12. Why can MVC become complicated on the web?
    - Network boundaries, client/server computation choices, state, database location, and rendering choices create many variations.

13. What is the key danger of patterns?
    - Applying a pattern before understanding the problem and pattern deeply.

14. What should an ADR capture?
    - The decision, context, tradeoffs, alternatives, and why the chosen path was appropriate.

## Last-Minute Memory Hooks

- Critical thinking beats technical vibes.
- Jargon is useful only when understood.
- Fashion changes fast; foundations change slowly.
- Architecture is choosing under constraints.
- Appropriate beats correct.
- Fight solution-first thinking.
- All tools trade something away.
- Client-server always involves client, server, and network.
- Localhost lies by making the network look easy.
- CRUD is easy to name, hard to model.
- REST: URLs are nouns, HTTP methods are verbs.
- MVC: Model, View, Controller, with many web variations.
- Patterns help only after understanding.
