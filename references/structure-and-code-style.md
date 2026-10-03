# Structure and Code Style

Adapted from Vue Contribution Rules, chapters 2, 3, 4.
Apply these conventions only to the authorized task; they do not authorize unrelated cleanup.

## Architecture and responsibility boundaries

- Organize application code around features, not a single collection of unrelated files.
- Keep feature-only components and logic inside their feature.
- Route-level pages coordinate the feature: data loading, navigation, actions, and composition of child components.
- UI components receive data and communicate changes through explicit contracts. Reusable UI must not embed a particular feature's business rules.
- API modules own transport concerns: endpoints, query serialization, request mapping, and response normalization.
- Utilities own framework-independent calculations and transformations.
- Composables own reusable Vue-reactive behavior and the cleanup associated with that behavior.
- Shared state, when needed, owns data whose lifetime or consumers extend beyond one component.
- Shared code must not depend on page implementations. Avoid circular dependencies and imports into another feature's private internals.
- Do not introduce a service, repository, factory, or other abstraction layer without a concrete responsibility that existing layers cannot reasonably handle.

## Directory structure and naming

### Default structure

The following tree is a placement guide, not a requirement to create every directory. `src/` represents the application's source root; do not relocate an existing project merely to match it.

```text
src/
├── api/
│   └── items.js
├── components/
│   └── ConfirmDialog/
│       ├── index.vue
│       └── style.css
├── composables/
│   └── useSelection.js
├── constants/
│   └── itemStatus.js
├── pages/
│   └── Items/
│       ├── index.vue
│       ├── style.css
│       ├── components/
│       │   └── ItemForm/
│       │       ├── index.vue
│       │       └── style.css
│       ├── pages/
│       │   └── Create/
│       │       └── index.vue
│       └── utils/
│           └── buildItemSummary.js
├── router/
│   └── routes.js
├── stores/
│   └── items.js
├── types/
│   └── item.ts
├── utils/
│   └── formatFileSize.js
└── messages/
    ├── en/
    │   └── items.json
    └── other-locale/
        └── items.json
```

- Create `stores/` only when actual shared stores are needed. Use the existing manager, or Pinia for greenfield/missing-manager setup within the requested scope; see [Pinia best practices](pinia-best-practices.md). Do not create empty stores.
- Create `types/` when shared TypeScript types are needed. JavaScript projects need not have it.
- Create `router/` and `messages/` only when those capabilities are used.
- Use `.ts` instead of `.js` where the project uses TypeScript, and `.scss` instead of `.css` where it uses SCSS.
- A component may use a colocated external stylesheet or an SFC style block. Follow one established approach within a module. Do not create empty stylesheets.
- For greenfield/missing-styling setup, use Tailwind within the requested scope. Utility-only components need no companion stylesheet; the tree's `style.css` files are optional. Preserve any existing styling method.

### Placement rules

- Route page: `pages/<Feature>/index.vue`.
- Nested route page: `pages/<Feature>/pages/<Page>/index.vue`.
- Feature-only component: `pages/<Feature>/components/<Component>/index.vue`.
- Shared component: `components/<Component>/index.vue`.
- Keep helpers close to their only consumer. Move them to shared directories when independent features genuinely need the same behavior and contract.
- Do not create a generic `modules/` or `helpers/` dumping ground. Name folders and files after their responsibility.

### Naming rules

| Item | Default convention | Example |
|---|---|---|
| Component and page folders | PascalCase | `ItemForm`, `Create` |
| Component name | PascalCase, descriptive | `ItemForm` |
| Component entry file | `index.vue` | `ItemForm/index.vue` |
| Component stylesheet | `style.css` or `style.scss` | `ItemForm/style.css` |
| JS/TS modules | camelCase | `formatFileSize.js` |
| Composables | `use` + PascalCase subject | `useSelection.js` |
| Functions and variables | camelCase | `fetchItems`, `selectedItem` |
| Boolean values | Meaningful predicate | `isLoading`, `hasChanges`, `canSubmit` |
| Fixed constants and enum-like keys | UPPER_SNAKE_CASE | `POLL_INTERVAL_MS`, `PENDING` |
| TypeScript types/interfaces | PascalCase | `Item`, `CreateItemPayload` |
| Custom application CSS classes | kebab-case BEM; framework utilities such as Tailwind are exempt | `item-form__field--invalid` |

- Include units in duration and size names when ambiguity is possible: `timeoutMs`, `sizeBytes`.
- Use meaningful domain names; avoid vague names such as `data2`, `temp`, or `handleStuff`.
- Keep spelling and casing consistent. Do not mix `Create` and `create` for the same kind of folder.

## Code formatting, imports, and exports

### Default formatting

- Use four-space indentation in JavaScript, TypeScript, Vue templates, and authored styles.
- Use single quotes in JavaScript/TypeScript and double quotes for template attributes.
- Use semicolons and trailing commas in multiline JavaScript/TypeScript structures.
- Keep lines near 100 characters where practical. Do not distort readable strings, URLs, or types just to meet a limit.
- Put multiline template attributes on separate lines. Keep short tags on one line only when readable.
- Self-close empty Vue components. Write native void elements according to the project's template lint rules.
- Use braces for control flow. Prefer early returns over deeply nested conditions.
- Use `const` by default and `let` when reassignment is necessary. Do not use `var`.
- Avoid nested ternaries and dense inline template expressions.

### Imports and exports

- Group imports consistently: external packages, shared application modules, feature-local modules, then styles.
- Use only aliases actually configured in the project. Use relative imports for nearby files when clearer.
- Prefer named exports for utilities, composables, and API operations.
- Use the default component export for Options API SFCs. A single cohesive configuration object may also use a default export.
- Do not add a default aggregate export merely to duplicate every named export.
- Remove unused imports and dead code. Do not retain commented-out implementations as history.

### Comments

- Explain intent, constraints, or a non-obvious decision—not what a straightforward statement already says.
- Document workarounds with the reason and, when available, a tracking reference.
- Keep comments accurate when behavior changes.
