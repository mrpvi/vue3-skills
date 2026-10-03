# Behavioral Evaluation Cases

Status: scenarios and candidate assertions authored; model-based behavior and
activation evaluations have not been run. This file is not a passing test report.

Use disposable fixtures outside the user's application repository. Do not install
the skill into an active agent configuration without approval.

## Evaluation procedure

1. Prepare each scenario with deterministic inputs and record initial file hashes.
2. Run the task with the skill and without it, using fresh sessions with the same
   model, tools, permissions, repository instructions, and fixture contents.
3. Preserve the output, tool trace, file diff, and executed check results for each run.
4. Review the first outputs and refine the candidate assertions below before
   grading. Base refinements on intended behavior, not on rewarding one output.
5. Grade with concrete evidence: changed files, relevant code, tool calls, and
   executed tests. A final claim alone is not proof.
6. Use a human review for readability and scope judgment not captured mechanically.
7. Revise the skill based on failures, rerun comparable cases, and record results.
8. Keep authored, executed, passed, failed, and blocked checks clearly distinct.

Suggested result fields: case ID, variant (baseline/skill), model, fixture version,
assertion, outcome, evidence, and limitations. Do not publish invented pass rates.

## Case 1 — Narrow Vue form fix

### Fixture

A JavaScript Options API component `ItemForm/index.vue` has a name field that
accepts whitespace-only text. Nearby `ItemList/index.vue` has deliberately messy
formatting. A separate file contains a pre-existing user edit. There is no test
runner; available read-only checks are documented in the fixture.

### Prompt

> Fix name validation so whitespace-only input is rejected. Edit only
> ItemForm/index.vue. Do not refactor or change surrounding files.

### Candidate assertions

- Only the authorized form file changes; the other files retain their hashes.
- The diff addresses validation and directly necessary feedback without unrelated
  import ordering, renaming, formatting, or component restructuring.
- Valid input remains accepted and whitespace-only input is rejected.
- Existing authoring style and public props/events remain intact.
- No dependency, lockfile, test runner, or new file is introduced.
- Available checks are reported truthfully; missing commands are not invented.
- Out-of-scope formatting issues are not fixed.

## Case 2 — Ownership decisions, planning only

### Fixture and prompt

> Plan state ownership for these Vue features without modifying files:
> 1. A single modal's open flag.
> 2. A wizard draft used by several deeply nested steps and discarded on exit.
> 3. An upload queue started on one route and displayed on unrelated routes;
>    this project already uses Pinia.
> 4. Two tables using the same selection behavior but independent selections.
> Explain the owner, lifetime, update contract, and cleanup responsibilities.

### Candidate assertions

- The modal flag remains component-local.
- The wizard uses a feature-root provider with reactive state and explicit owner
  actions; the answer does not introduce an application-wide store for its draft.
- The queue uses the existing managed store/application owner and defines cleanup
  independently of the initiating page's unmount.
- The tables call a composable separately; no accidental singleton is introduced.
- The answer distinguishes store lifetime from browser-refresh persistence.
- The planning-only request produces no file edits or installations.

## Case 3 — JavaScript and plain CSS

### Fixture

An existing Vue project uses JavaScript Options API, plain CSS, and no store.
`ItemList/index.vue` and `ItemList/style.css` already exist. Stable primitive IDs
are available. The fixture has an established way to load the colocated stylesheet.

### Prompt

> Add local item selection with a clear-selection action. Extract independent
> selection behavior into useSelection.js. Use BEM for the new classes. Limit
> changes to the list, its stylesheet, and the new composable.

### Candidate assertions

- Only the authorized files are created or changed.
- Each composable invocation has independent state and returns reactive values
  plus explicit actions; no mutable module-level singleton is added.
- Selection toggles correctly and clearing resets selection.
- The component uses its existing Options API style and stable list keys.
- Application-owned new classes follow BEM; existing third-party classes are not
  renamed. No SCSS, TypeScript, Pinia, or new dependency is introduced.
- The example's import paths and CSS variables are not copied without checking
  that they resolve in the fixture.
- The final response distinguishes executed checks from unexecuted reasoning.

## Case 4 — Clean Code must not expand a narrow fix

### Fixture and prompt

A Vue form contains a long but cohesive submit method, a useful business-rationale
comment, and an intentional nullable selection. Another component has poor naming.

> Fix only the whitespace validation in this Vue form. Apply Clean Code principles
> to the requested fix, but do not change unrelated behavior or other files.

### Candidate assertions

- Only the authorized validation changes; nearby code and other files are preserved.
- The agent does not use the Boy Scout Rule as permission for opportunistic cleanup.
- No arbitrary function-length or argument-count limit drives unrelated extraction.
- The meaningful comment and nullable contract are retained.
- No test framework, dependency, class hierarchy, or state library is introduced.

## Case 5 — Explicit readability review is read-only

### Prompt

> Review this Vue composable using Clean Code ideas. Identify confusing names,
> hidden side effects, and failure handling. Do not edit files.

### Candidate assertions

- The agent loads the Clean Code adaptation and the relevant Vue reference.
- Findings cite concrete code and consequences, distinguishing defects from
  maintainability suggestions and preferences.
- It does not report a coherent 25-line function as defective solely due to length.
- It preserves the distinction between an empty successful response and a failed request.
- No files change; recommendations are not silently implemented.

## Case 6 — Greenfield defaults

### Fixture and prompt

An empty disposable project, compatible with Vue 3 and Tailwind v4, has no stack
constraints. Dependency installation is authorized in this fixture only.

> Set up a new Vue app with shared cart state used by unrelated views and a
> responsive cart summary. Use the contribution skill's defaults.

### Candidate assertions

- Pinia and Tailwind are selected, with no unnecessary stack-choice question.
- Pinia is registered before consumers; the cart has domain actions and reset rules.
- A local disclosure flag stays local rather than entering the cart store.
- Tailwind v4 uses the correct build integration, CSS import, and CSS-first theme.
- Templates use complete detectable utility names, semantic controls, and focus states.
- No TypeScript, SCSS, UI library, or test runner is forced merely by the skill.
- Only necessary setup files change; build/render checks are reported accurately.

## Case 7 — Missing manager only

### Fixture and prompt

An existing Vue app uses SCSS/BEM with no managed shared state. The user authorizes
necessary dependency/startup changes for a new cross-route queue.

> Add a shared queue that survives route changes. Preserve the existing styling.

### Candidate assertions

- Pinia is chosen and its best-practices reference is loaded.
- Existing SCSS/BEM is preserved; no Tailwind dependency/configuration is added.
- Queue lifetime, cleanup, loading/error state, and context reset are explicit.
- Store state/getters use the store object or `storeToRefs`, not snapshot destructuring.
- Component-only state remains local; setup edits are limited to necessary files.

## Case 8 — Missing styling only

### Fixture and prompt

An existing Vue app has Vuex and no authored styling or UI styling library.
Styling setup is authorized.

> Add styling for this responsive feature, keeping our current state manager.

### Candidate assertions

- Tailwind is selected after checking build/browser compatibility.
- Vuex is preserved; no Pinia dependency, parallel store, or migration is introduced.
- CSS entry/build integration are changed only as required for the styling setup.
- Custom authored selectors, if any, use BEM; Tailwind utilities are not renamed.

## Case 9 — Existing methods and versions are authoritative

### Fixtures and prompt

Run separately with (a) Vuex + plain CSS, (b) Pinia + scoped SCSS, and
(c) Pinia + Tailwind v3 with existing configuration.

> Add one responsive loading indicator using this project's existing conventions.

### Candidate assertions

- No state-manager/styling-method migration or additional competing system occurs.
- Plain CSS and scoped SCSS are recognized as styling methods, not missing Tailwind.
- The v3 fixture retains its setup and uses v3-compatible syntax, not v4 imports
  or directives. No package/config changes appear merely to modernize it.
- An existing Pinia installation is not a reason to globalize the loading flag.

## Case 10 — Missing infrastructure does not widen a fix

### Fixture and prompt

A Vue component has a date-formatting defect. The app has no state manager and
no established styling method.

> Fix this date label. Edit only DateLabel/index.vue; no other changes.

### Candidate assertions

- Only the directly relevant label logic changes.
- Neither Pinia nor Tailwind is installed/configured, and no styles/store are added.
- Missing infrastructure is not presented as a blocker for the unrelated fix.

## Case 11 — Explicit stack constraint, planning only

### Prompt

> Plan a feature-based directory structure for a new Vue app using JavaScript
> and plain CSS, with no store library. Do not create files.

### Candidate assertions

- Explicit user constraints override fallback choices; Pinia and Tailwind are
  not imposed. This is the expected behavior for trigger prompt P08 too.
- The plan remains read-only and uses BEM for custom classes.
- The result does not assert that the default stack is mandatory for every Vue app.

## Case 12 — Pinia reactivity and isolation

### Fixture and prompt

An existing Pinia app has a cart view that destructures `itemCount` directly from
its store. The fixture includes a runner and a fresh Pinia instance per test.

> Fix this cart count so it updates when items are added. Preserve the store API.

### Candidate assertions

- The fix uses `storeToRefs()` or accesses the getter through the store object.
- Bound actions can remain destructured; no incorrect blanket binding fix appears.
- An executed test observes the count after an action, using real actions rather
  than default action stubs. Independent test instances do not share cart state.
- No unrelated state migration, persisted-state plugin, or dependency upgrade occurs.

## Trigger evaluation

Use `evals/trigger-cases.json` from the skill root. It contains 30 prompts:
15 positives, 15 near-miss negatives, split 60% train / 40% validation with
balanced labels. Run each prompt three times in a compatible isolated discovery
environment. Observe actual activation in traces. Tune against train cases and
select descriptions using validation results, without rewriting prompts to hide
failures. Manual description inspection is not an activation test.

## Package checks versus behavioral evidence

Frontmatter parsing, resolved links, chapter coverage, and balanced code fences
validate packaging. Executing the bundled composable example can verify that
example's behavior. Neither demonstrates that an agent will reliably activate
the skill or respect edit scope; only recorded agent runs can support that claim.
