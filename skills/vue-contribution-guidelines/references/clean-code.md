# Clean Code for Vue Contributions

An adapted guide to applying selected Clean Code ideas to Vue, JavaScript, and
TypeScript. This is an original practical synthesis, not the book's text or a
complete chapter library.

## Sources and limits

- Conceptual source: *Clean Code: A Handbook of Agile Software Craftsmanship*,
  Robert C. Martin, with contributing authors (2008).
- Reviewed companion: Ariana Maghsoudi's
  [clean-code-book skill](https://github.com/ariana126/flfl/tree/main/plugins/bookshelf/skills/clean-code-book),
  specifically its `SKILL.md`, `cheatsheet.md`, and `patterns.md`.
- The companion declares MIT licensing in its skill frontmatter. This adaptation
  does not reproduce its files or imply that the published book is MIT-licensed.
- The original material uses many Java-oriented examples. Apply the underlying
  reasoning to Vue; do not transplant Java classes, inheritance, locking, or
  checked-exception rules into a JavaScript application.

## Authority and scope

The skill's AI Interaction Guidelines, authorized task scope, and explicit project
contracts take precedence over these supporting heuristics. This reference does
not authorize cleanup, file edits, additional dependencies, or architectural changes.

- Write the requested change clearly; do not clean surrounding code opportunistically.
- Use refactoring techniques only when refactoring is requested or approved, or
  the particular change is directly necessary to implement the authorized behavior.
- If a necessary refactor expands scope, explain the dependency and ask first.
- In review-only work, identify evidence-backed problems; do not apply fixes.
- Treat function length, argument count, and similar thresholds as warning signs,
  not automatic failures or commands to split code.

## 1. Names should express domain intent

- Name values after what they represent and operations after what they do.
- Use one term for one domain concept. Include units where ambiguity matters.
- Prefer a meaningful predicate such as `canSubmit` over a repeated opaque condition.
- Do not rename established public props, events, exports, or route names merely
  to match a stylistic preference. Those names are contracts.
- For existing code, apply naming improvements only within the authorized change.

## 2. Functions should have a coherent responsibility

- Keep the steps of a function at a comprehensible level of abstraction.
- Separate transport mapping, validation, domain decisions, and UI feedback when
  doing so gives each operation a clearer owner.
- Extract a helper when it represents a useful concept, not merely to get below
  an arbitrary line limit. Too many trivial helpers can make reading harder.
- Use guard clauses to simplify exceptional or ineligible paths without hiding
  important behavior.
- Prefer a named options object when positional arguments are difficult to read.
  Do not invent an options abstraction for a simple, obvious operation.
- A boolean option is not inherently wrong. Separate operations when a flag
  selects unrelated responsibilities; preserve sensible boolean props/options.

## 3. Make side effects visible

- A computed getter or validation predicate should answer a question without
  starting requests, navigating, or mutating unrelated state.
- Name state-changing operations as actions and keep mutation ownership explicit.
- Command/query separation is a useful design preference, not a ban on a create
  operation returning its newly created resource or an async action returning a result.
- Pass dependencies explicitly when that makes ownership and tests clearer. Do
  not add a dependency-injection container or wrapper around every imported utility.

## 4. Comments should preserve reasoning

- Prefer expressive code over comments that translate each line into prose.
- Keep comments that explain a business constraint, compatibility workaround,
  non-obvious race, or reason a simpler-looking approach is incorrect.
- Do not treat all comments as defects or delete rationale to make code shorter.
- Remove stale comments or dead code only when they belong to the authorized
  change. Do not turn this rule into a repository cleanup instruction.

## 5. Cohesion matters more than abstraction count

- Keep components focused on their UI responsibility and composables focused on
  a coherent reactive behavior.
- Extract duplicated business knowledge when it truly represents the same rule.
  Similar markup from unrelated features does not necessarily require one component.
- Prefer ordinary functions, modules, and Vue composition over introducing class
  hierarchies to imitate Java examples.
- Keep boundary adapters where they isolate meaningful contract differences.
  A pass-through wrapper with no owned policy may add indirection without benefit.
- Do not automatically replace a clear `switch` or mapping with polymorphism.

## 6. Errors and absence must remain distinguishable

- Preserve the distinction between failure, a successful empty collection, and
  an intentionally absent value.
- Do not convert a failed request to `[]` merely to avoid handling an error.
- `null` and `undefined` are legitimate when they accurately express the contract.
  Handle them explicitly rather than creating artificial placeholder objects.
- Follow the project's established error contract, whether exceptions or explicit
  result objects. Do not convert all errors to one style as incidental cleanup.
- Attach useful, non-sensitive context and let the appropriate owner present the
  error once. Restore pending state and release resources on every exit path.

## 7. Test behavior and refine incrementally

- Prefer focused tests with clear setup, action, and assertions for one behavior.
  Several assertions may be appropriate for that behavior.
- Keep tests independent and repeatable. Avoid arbitrary real-time waits when
  the existing test tools can control timers or request completion.
- Use an existing test framework; do not install one without approval. If none
  exists, report which checks or manual scenarios were used and their limitations.
- TDD can help when the project supports it; it is not permission to block a small
  task on installing tooling or to claim unexecuted tests have passed.
- For an approved refactor, establish current behavior, make small changes, and
  rerun relevant checks. Preserve public contracts unless changing them is authorized.

## 8. Adapt concurrency advice to Vue async work

- Separate async coordination from presentational components when the workflow
  genuinely needs its own owner.
- Account for overlapping requests, stale responses, cancellation, repeated user
  actions, and cleanup when a component or effect scope is disposed.
- A single JavaScript event loop does not eliminate logical races across `await`.
- Do not apply thread-locking patterns unless the application actually uses a
  concurrency mechanism requiring them; avoid unrelated synchronization abstractions.

## Focused review procedure

1. Confirm the requested behavior and permitted files before judging cleanliness.
2. Inspect only relevant names, responsibilities, side effects, contracts, and
   failure paths. Do not widen a targeted review into a whole-project audit.
3. Identify a concrete consequence: ambiguity, hidden mutation, duplicated policy,
   incorrect error handling, or unnecessarily coupled behavior.
4. Distinguish a correctness defect from a maintainability recommendation and a
   personal preference. Explain the consequence rather than citing a threshold alone.
5. Propose the smallest useful change. Ask before expanding scope; do not silently
   apply recommendations made during a review-only task.
6. Verify applicable behavior using existing checks and report what actually ran.

## Quick conflict decisions

| Companion idea | Apply in this Vue skill |
|---|---|
| Leave all touched code cleaner | Keep the requested change clean; report unrelated cleanup separately |
| Very short functions and minimal arguments | Prefer coherent responsibilities and readable calls; no hard numeric limits |
| Avoid comments | Avoid redundant narration; retain valuable rationale and constraints |
| Never return null | Represent absence honestly and distinguish it from failure |
| Remove all duplication | Share the same business rule, not merely similar-looking code |
| Wrap every external API | Add a boundary only when it owns a useful adaptation or policy |
| Use classes/inheritance for extension | Prefer idiomatic Vue/JS composition unless the existing design justifies classes |
| Always begin with a failing unit test | Use the approved test workflow and be honest about missing tooling |
| Refactor whenever a smell is found | Refactor only within authorized scope; review-only means no edits |
