# Vue Components

Adapted from Vue Contribution Rules, chapters 5.
Apply these conventions only to the authorized task; they do not authorize unrelated cleanup.

## Vue component structure and contracts

### Authoring style

- Prefer Options API as the default for new projects adopting this guide's coding style.
- If an existing project or module deliberately uses Composition API, follow that convention. Do not convert component styles as part of unrelated work.
- In Options API components, keep `setup()` focused on integration with composables or stores that require it. Avoid splitting the same feature logic arbitrarily between APIs.
- For Options API, use a predictable order: `name`, `components`, `directives`, `mixins`, `props`, `emits`, `setup`, `data`, `computed`, `watch`, lifecycle hooks, `methods`. Follow configured ordering for additional options.
- Give components descriptive names. Prefer multi-word names over ambiguous names such as `List` or `Form`.

### Props, events, and slots

- Declare props with appropriate types, required flags, and defaults. Use factory functions for mutable object/array defaults.
- Declare emitted events explicitly.
- Treat props as read-only, including nested objects. Emit a change or use an explicitly agreed ownership contract rather than mutating parent state implicitly.
- Use `update:<propName>` for a `v-model` contract; use semantic events such as `submit`, `cancel`, `retry`, and `select` for actions.
- Use kebab-case for custom multi-word event names. Follow Vue's `update:<propName>` convention for model events.
- Keep event payloads small, meaningful, and stable.
- Use slots for genuinely variable content rather than accumulating unrelated boolean props.

### Reactivity and templates

- Use computed values for derived state. Keep computed getters free of side effects.
- Use watchers for side effects triggered by changes, not for maintaining a value that could be computed.
- Prefer named computed values or methods over business logic embedded in templates.
- Use stable item identifiers as list keys. Avoid array-index keys for mutable or reorderable lists.
- Do not access or modify the DOM directly when Vue bindings or component contracts can express the behavior.
- Pair subscriptions, listeners, observers, timers, and other resources with lifecycle cleanup.
