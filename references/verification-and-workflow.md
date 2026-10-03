# Verification and Workflow

Adapted from Vue Contribution Rules, chapters 15, 16.
Apply these conventions only to the authorized task; they do not authorize unrelated cleanup.

## Verification and contribution workflow

- Keep commits and review requests focused and understandable.
- Follow the repository's documented branch and release process. Do not assume a particular branch name, hosting service, or deployment platform.
- Use concise imperative commit subjects. Use one consistent prefix convention when the repository defines it.
- Describe the behavior changed, the reason, important trade-offs, and how it was verified.
- Update relevant documentation and change records when user-facing behavior or contracts change.
- Run the checks that actually exist. Do not claim that an absent test or type-check command passed.
- When tests exist, add or update regression coverage for the changed behavior. Do not introduce a test framework without agreement, but do not treat the absence of tests as a prohibition on future testing.
- Check relevant success, empty, error, invalid-input, and pending-action paths.
- For affected features, also verify narrow layouts, keyboard operation, localization/RTL, navigation, and asynchronous cleanup.
- Inspect dependency upgrades for affected contracts; do not assume changing the version number completes the migration.
- Distinguish new failures from pre-existing failures. Report both accurately without expanding the change unnecessarily.

### Definition-of-done checklist

- [ ] Files are placed according to responsibility and scope.
- [ ] Names, component contracts, and BEM classes follow the conventions.
- [ ] State and business rules have explicit owners.
- [ ] API mapping and error handling remain at appropriate boundaries.
- [ ] Relevant loading, empty, failure, and success states are handled.
- [ ] Listeners, timers, and obsolete async work are cleaned up.
- [ ] Relevant routes, translations, types, mocks, and documentation are updated.
- [ ] Available checks and relevant manual scenarios were run, or omissions explained.
- [ ] The final diff contains no unrelated changes or sensitive information.

## Working rules for developers and agents

- Read repository instructions and inspect the relevant implementation before editing.
- Find a comparable, current example; verify it follows the intended convention rather than blindly copying legacy code.
- Inspect actual package APIs, exports, scripts, and configuration. Do not invent component props, events, imports, aliases, or commands.
- Ask before introducing a dependency, changing an architectural convention, changing a public contract, or performing an unrelated migration.
- Ask when unresolved requirements materially change the result. Do not ask for approval for routine implementation choices already covered by these rules.
- Preserve unrelated edits and untracked user files. Do not overwrite existing work without inspecting it.
- Do not add broad abstractions, global state, or configuration options that the requested behavior does not need.
- Do not disable checks to hide newly introduced problems.
- Do not commit, push, release, deploy, or publish without the required authorization.
- Finish with a concise account of what changed, which checks ran, their actual results, and any remaining limitations.

### Common placement decisions

| Need | Placement |
|---|---|
| UI used only by one feature | That feature's `components/` |
| UI genuinely shared across features | Shared `components/` |
| Pure feature-only transformation | A helper inside the feature |
| Pure cross-feature transformation | Shared `utils/` |
| Reusable Vue-reactive behavior | A composable near its consumers; shared only when needed |
| Network operation | Domain API module |
| Page-only form or modal state | Owning component |
| State with shared consumers/lifetime | Established shared-state mechanism |
| Repeated domain status or fixed limit | Domain constants |
| Shared TypeScript domain contract | `types/`, when TypeScript is used |

### New-feature checklist

1. Identify the feature's owner, folder, and component boundaries.
2. Define data contracts and API operations where needed.
3. Implement state, validation, and asynchronous behavior with clear ownership.
4. Add components and colocated BEM styles without unnecessary shared abstractions.
5. Register routes, navigation, translations, and feature checks only when applicable.
6. Verify the complete user flow and relevant failure paths.
7. Review the diff and report the checks performed.
