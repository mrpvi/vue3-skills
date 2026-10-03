# Data, Forms, and Async

Adapted from Vue Contribution Rules, chapters 8, 9, 10.
Apply these conventions only to the authorized task; they do not authorize unrelated cleanup.

## Data access and data contracts

- Put network operations in domain-focused API modules, not inline in templates or UI event handlers.
- Reuse the project's configured HTTP client. Do not create duplicate clients or authentication mechanisms without a justified requirement.
- API functions own endpoint construction, query serialization, and transport-specific headers/options.
- Components and application logic should normally work with camelCase names. Convert casing at the API boundary when the server contract requires it.
- Do not blindly convert opaque dictionaries, user-defined keys, signed fields, or payloads whose exact spelling must be preserved.
- Give operations descriptive names such as `getItems`, `createItem`, and `deleteItem`.
- Define one owner for error presentation. Avoid duplicate global and component-level notifications for the same failure.
- Do not silently turn a failed request into a successful empty result unless that fallback is intentional and documented.
- When mocks are used, keep their request and response contracts aligned with the API operations being changed. Do not substitute mocks for production behavior silently.

### When using TypeScript

- Type API inputs and returned domain data explicitly.
- Use shared domain types when multiple consumers need the same contract. Keep implementation-only types close to their consumer.
- Distinguish request DTOs, response DTOs, and domain models when their shapes differ.
- Prefer `unknown` plus narrowing over unexamined `any` for uncertain external values.
- Model nullability and optional fields honestly. A type assertion does not validate runtime data.
- Do not introduce TypeScript into an otherwise JavaScript module as an unrelated style change.

## Forms, validation, and business rules

- Keep form values, validation state, submission state, and server errors distinguishable.
- Reuse the project's existing validation approach. Do not add a second library for one form.
- The component owning the business decision must own its domain-specific validation. A reusable input must not know which product action it serves.
- Child forms may own field-level validation, but must expose validity and submission behavior through an explicit contract.
- Validate at submission time as well as through field feedback. Disabled buttons alone are not validation.
- Show actionable field errors near the relevant field; keep operation-level failures separate.
- Prevent duplicate submissions while an operation is pending.
- Clear dependent values and stale errors deliberately when their inputs change.
- Reuse a create/edit form when the fields and behavior genuinely match. Keep mode differences explicit.
- After success, explicitly decide whether to reset, close, refresh, or navigate.

## Asynchronous operations and UI states

- Model initial loading, background refreshing, and action-specific pending states separately when their UI behavior differs.
- Distinguish an empty successful response from a failed request.
- Use `try` / `catch` / `finally` so pending state is restored on success and failure. Propagate failures when another layer owns their handling.
- Define retry behavior intentionally. Do not blindly retry non-idempotent actions that could create duplicate effects.
- For overlapping searches or filter requests, ensure an older response cannot overwrite newer results. Use cancellation or a request-identity guard.
- Debounce rapid user input where appropriate and cancel outstanding debounced work during cleanup.
- Poll only while needed. Define terminal states, avoid uncontrolled overlapping requests, and clear timers when their owner is disposed.
- Cancel obsolete work when supported. Otherwise, prevent obsolete results from mutating current state.
- Keep long-running workflow orchestration out of presentational components.
- Preserve useful visible data during background refresh when possible rather than repeatedly replacing it with a full-page loading state.
