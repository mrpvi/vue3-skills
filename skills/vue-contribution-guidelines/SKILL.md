---
name: vue-contribution-guidelines
description: >
  Use this skill when creating, modifying, or reviewing Vue components,
  pages, composables, forms, state management, API integrations, or CSS
  in a Vue project. Apply consistent directory structure, naming,
  component contracts, BEM styling, state ownership, and pragmatic Clean Code
  principles for readable functions, naming, boundaries, and error handling.
  For greenfield Vue setup, default to Pinia and Tailwind. In existing projects,
  choose Pinia only if no state manager exists and Tailwind only if no styling
  method exists, when that setup is within the requested scope.
  Use it for targeted Vue bug fixes as well as new features, without expanding
  the requested scope or migrating established project conventions.
---

# Vue Contribution Guidelines

Apply a consistent coding style to Vue tasks without turning a focused request
into a repository-wide cleanup. Pinia and Tailwind are conditional defaults for
unconfigured capabilities, not mandatory migrations. TypeScript, SCSS, routing,
localization, and any particular UI library remain optional.

## AI Interaction Guidelines — read first

- Work only on the requested task. Its acceptance criteria define the boundary.
- Reading surrounding files for context does not authorize modifying them.
- If the user gives an exclusive file list, edit only those files. If a necessary
  change falls outside it, explain the blocker and ask before proceeding.
- Otherwise identify the smallest directly necessary file set and edit only the
  relevant sections. Scope applies within a file as well as between files.
- Do not perform incidental refactoring, renaming, reformatting, import sorting,
  dead-code removal, optimization, comment rewrites, or dependency upgrades.
- General quality guidance below is not authorization for unrelated cleanup.
- Do not convert Options API to Composition API, JavaScript to TypeScript,
  CSS to SCSS, or local state to a store without an explicit request or approval.
- Do not opportunistically change lockfiles, generated files, configurations,
  snapshots, translations, or documentation. Change them only when directly
  required and within the authorized scope.
- A requested greenfield setup or setup of a missing capability uses the defaults
  below within its authorized scope. Ask before other dependency additions,
  architectural or public-contract changes, or scope expansion. Choosing a
  default does not bypass tool permissions or installation authorization.
- Report unrelated problems separately; do not fix them without approval.
- Inspect the target and preserve existing edits and untracked user files.
- Prefer targeted edits over whole-file rewrites.
- Prefer read-only checks. Do not run broad auto-fix, formatting, generation, or
  upgrade commands that might modify files outside the authorized scope.
- Before a command writes files, understand and restrict its outputs. Ask if
  the output cannot be restricted to the approved scope.
- Review the final diff without discarding pre-existing user changes.
- Do not commit, push, publish, install, release, deploy, or perform destructive
  actions without the required authorization.
- Review and planning requests are read-only unless the user also asks for edits.

## Apply the rules in context

- For new code in a new project, use this skill's defaults.
- In an existing project, inspect explicit repository instructions and actual
  tooling first. Surface conflicts instead of silently overriding an enforced
  convention or weakening checks.
- A legacy example does not automatically establish the preferred pattern.
  Choose a current, comparable example and verify its contract.
- Prefer explicit state ownership, feature isolation, and readable code over
  cleverness or speculative abstractions.
- Apply the independent Pinia/Tailwind selection gates in step 3. Other optional
  technologies apply only when already used or their introduction is approved.
- If unresolved requirements materially affect behavior or scope, ask first.
  Do not ask about routine choices already settled by these conventions.

## Workflow

### 1. Establish the task and boundary

- Identify whether this is implementation, a bug fix, review, or planning.
- Identify requested behavior, acceptance criteria, and any permitted file list.
- Record existing user edits before changing files, using available read-only
  inspection tools. Do not assume a clean worktree.
- Find only the context needed to complete the task; do not launch a broad audit.

### 2. Inspect the project and select references

Check repository instructions, relevant package/configuration files, and a
comparable implementation. Verify actual APIs, exports, aliases, and commands;
do not invent them.

Load the references relevant to the requested change before working on it:

| When the task involves… | Read |
|---|---|
| New files, feature placement, naming, imports, or formatting | [Structure and code style](references/structure-and-code-style.md) |
| SFC structure, props, events, v-model, watchers, or lifecycle | [Vue components](references/vue-components.md) |
| Local state, props/events, provide/inject, stores, or composables | [State and composables](references/state-and-composables.md) |
| Greenfield/missing-manager setup or existing Pinia store code | [Pinia best practices](references/pinia-best-practices.md) |
| Greenfield/missing-styling setup or existing Tailwind code | [Tailwind best practices](references/tailwind-best-practices.md) |
| Requests, validation, form submission, loading, polling, or cancellation | [Data, forms, and async](references/data-forms-and-async.md) |
| CSS/BEM, localization, routing, configuration, or destructive actions | [Styling and app integration](references/styling-and-app-integration.md) |
| Choosing checks, reviewing work, or completing a contribution | [Verification and workflow](references/verification-and-workflow.md) |
| Clean Code review, function responsibilities, naming clarity, side effects, or an approved refactor | [Clean Code for Vue](references/clean-code.md) |

Read multiple references for a cross-cutting task, but do not load the whole
library for a small edit. Read `references/clean-code.md` when the task explicitly
requests clean-code guidance, names a Clean Code concept, asks for a readability
review, or approves a refactor that needs those techniques. Do not load it as a
reason to expand an ordinary Vue fix. Reference examples illustrate conventions;
their paths, labels, variables, and commands are not assumed to exist in the project.

### 3. Select technology defaults, then choose placement and ownership

Evaluate state management and styling independently:

| Project condition | State-management choice | Styling choice |
|---|---|---|
| Greenfield Vue project or an explicitly requested new setup | Use Pinia for shared application state; keep component-only state local | Use Tailwind for new styling, following the project's compatible Tailwind version |
| Existing project with no state manager | Use Pinia when the requested work needs managed shared state or the project is being set up; do not create a store merely to move local state | Keep the styling decision independent |
| Existing project with no styling method | Keep the state-management decision independent | Use Tailwind for requested application styling or project setup |
| Existing manager or styling method is present | Preserve it and follow its conventions | Preserve it and follow its conventions |
| Narrow bug fix unrelated to missing infrastructure | Do not install, migrate, or configure Pinia or Tailwind as incidental work | Do not install, migrate, or configure Pinia or Tailwind as incidental work |
| Explicit user or repository constraint | Follow the explicit constraint, or surface the conflict before editing | Follow the explicit constraint, or surface the conflict before editing |

Plain CSS, SCSS, CSS Modules, scoped SFC styles, and an established component
library all count as styling methods. A local ref, provider, or composable is not
necessarily an application state manager; inspect the project's actual ownership
model before deciding that Pinia is missing. If adding a default requires package,
configuration, or lockfile changes, treat those as part of the requested setup and
validate them; never perform them during an unrelated fix.

Use the smallest correct owner:

1. One component needs state → local state.
2. A parent and a few children/siblings need it → common parent, props/events.
3. A deeply nested feature subtree needs context → feature-root provide/inject.
4. Unrelated routes/features share state, or a workflow outlives its page → the
   established managed store or application-level owner.
5. Repeated reactive behavior with independent values → separate composable calls.

Read the full state decision table when ownership is part of the task. Treat
URL state and durable persistence as separate decisions.

Keep feature-only components and helpers in their feature. Promote them to shared
code only when independent consumers genuinely need the same contract.

### 4. Perform only the authorized work

- Implementation: make the smallest change meeting the acceptance criteria.
- Review: report verified findings with locations; do not apply fixes.
- Planning: propose concrete steps and scope; do not create implementation files.
- If a true prerequisite requires expanding scope, stop and explain the smallest
  additional change. A nearby lint warning or code smell is not a prerequisite.
- Do not install optional tools to make the project resemble the examples. The
  Pinia and Tailwind defaults are exceptions only for greenfield setup or a
  requested setup that genuinely lacks that capability; they do not authorize
  unrelated installation or migration.

### 5. Validate the result

- Use the checks that actually exist and are relevant to the change.
- Follow the verification reference for applicable success, empty, error,
  pending, invalid-input, cleanup, layout, and navigation cases.
- Inspect the final diff for unrelated edits, changed contracts, or sensitive data.
- Fix defects introduced within the authorized task, then rerun relevant checks.
- If verification exposes an out-of-scope prerequisite, ask rather than expanding
  the task automatically.
- Distinguish new failures, pre-existing failures, and checks not run.

### 6. Report accurately

Keep the response proportional to the task. For implementation, use:

```text
Changed: [requested behavior and modified files]
Verified: [commands/scenarios and actual results]
Not run / blocked: [only if applicable, with reasons]
Out of scope: [important findings requiring separate approval, if any]
```

For review, report evidence-backed findings and limitations without claiming edits.
For planning, report proposed steps and unresolved decisions without claiming work
has been implemented. Do not claim completion if an essential requested part is
missing or blocked.

## Gotchas

- Options API is the new-project default, not permission to rewrite existing
  Composition API components.
- A composable called twice normally creates two independent states. Shared
  behavior is not the same as one shared state instance.
- provide/inject makes an ancestor-owned dependency available to descendants;
  it does not automatically persist state or cancel background work.
- A store is not required simply because data came from an API, and installing
  a store does not make state survive refresh.
- Read-only injected state plus owner actions makes mutation ownership explicit.
- BEM applies to application-authored classes in CSS or SCSS. Tailwind utility
  classes are the explicit framework-provided exception; do not rename them into
  BEM. Custom classes added alongside Tailwind still use BEM.
- The sample directory tree is a placement guide, not permission to relocate
  existing source code or create unused directories.
- Convert API casing only where the contract permits it; preserve opaque keys
  and signed values.
- Never assume example aliases, translations, tokens, libraries, or scripts exist.
- Do not run repository-wide fixes to satisfy a narrowly scoped task.
- Clean Code advice is supporting guidance, not permission for opportunistic
  cleanup. The AI scope rules and explicit project contracts remain authoritative.
- Do not enforce arbitrary function-size limits, ban meaningful null values or
  useful comments, or introduce Java-style class hierarchies in the name of Clean Code.

## Maintainer evaluation resources

These are for evaluating this skill, not prerequisites for ordinary Vue tasks:

- [Trigger cases](evals/trigger-cases.json): activation and near-miss prompts.
- [Behavior cases](evals/behavior-cases.md): isolated scenarios, assertions,
  baseline comparison procedure, and rules for reporting evaluation results.

The core references are adapted from the approved Vue Contribution Rules.
The Clean Code reference is an attributed, Vue-oriented synthesis informed by
the linked book-companion skill; see its Sources and limits section.
All references are self-contained; no original Desktop file, network fetch,
separately installed book skill, or project-specific resource is required at runtime.
