# Styling and App Integration

Adapted from Vue Contribution Rules, chapters 11, 12, 13, 14.
Apply these conventions only to the authorized task; they do not authorize unrelated cleanup.

## Styling and CSS conventions

### Choose the styling method first

For greenfield setup or requested styling when no styling method exists, use
Tailwind and read [Tailwind best practices](tailwind-best-practices.md). Existing
plain CSS, SCSS, CSS Modules, scoped SFC styles, or component-library styling are
valid established methods: do not replace or supplement them with Tailwind
without a request. Absence of Tailwind alone does not trigger this default.
Existing Tailwind projects keep their installed version; no automatic v4 migration.

### BEM is the custom class-naming convention

Tailwind utility classes are framework-provided and exempt from BEM. Do not
rename them or create BEM wrappers solely to satisfy this rule. When defining
custom application CSS classes, including custom styles alongside Tailwind, use:

```text
block
block__element
block--modifier
block__element--modifier
```

- Name the block after the component or cohesive UI responsibility.
- Use kebab-case for every part of the class name.
- Keep block names distinctive enough to avoid collisions.
- Apply modifier classes together with the base class.
- Do not encode DOM depth into class names such as `block__header__title`. Use `block__title`.
- Do not rename third-party classes to force BEM. This rule applies to classes the application owns.

```vue
<template>
    <form class="item-form" :class="{ 'item-form--submitting': isSubmitting }">
        <label class="item-form__label" for="item-name">Name</label>
        <input
            id="item-name"
            v-model="name"
            class="item-form__input"
            :class="{ 'item-form__input--invalid': hasNameError }"
        >
    </form>
</template>
```

### CSS and SCSS

Plain CSS is sufficient:

```css
.item-form__input {
    width: 100%;
}

.item-form__input--invalid {
    border-color: var(--color-error);
}
```

When using SCSS, keep nesting shallow and preserve the same generated class names:

```scss
.item-form {
    &__input {
        width: 100%;

        &--invalid {
            border-color: var(--color-error);
        }
    }
}
```

`--color-error` is illustrative: reuse the project's defined variable or an appropriate existing value rather than assuming this variable exists.

### Style ownership

- Colocate styles with their owner. Avoid placing feature-only rules in global stylesheets.
- Use classes for component styling. Avoid IDs, broad element selectors, and long descendant chains.
- Scoped styles do not replace meaningful BEM names.
- Prefer state classes and Vue bindings over imperative DOM styling.
- Avoid `!important` and deep overrides. If required, keep the override narrow and explain why.
- Reuse established variables for repeated values when available. Do not require a token system or preprocessor solely for this guide.
- Do not add SCSS compilation or custom class-name helpers merely to produce BEM classes.
- Ensure layouts work at narrow widths and do not create accidental page-level horizontal scrolling.

## User-facing content and localization

- Use consistent terminology for the same concept across labels, actions, errors, and documentation.
- Error messages should explain what happened and, when possible, what the user can do next.
- Do not use display labels as identifiers or business-state values.
- When localization is supported, put visible copy in translation resources and group keys by feature.
- Add corresponding keys for all supported locales. Do not silently copy one language into another locale and present it as translated.
- Use interpolation and pluralization rather than concatenating translated fragments.
- Localize displayed numbers and dates without changing the raw values used for calculations or API requests.
- For RTL interfaces, treat technical values such as URLs, paths, and identifiers as LTR when needed. Use logical CSS properties where direction should follow the locale.
- Centralize locale-dependent links when the project supports them.

## Routing and navigation

When the project uses routing:

- Keep route definitions in the established routing layer and lazy-load feature pages when supported.
- Use descriptive, unique route names. Prefer dot-separated feature namespaces with camelCase segments: `items.list`, `items.create`, `items.details`.
- Prefer named navigation over repeated hard-coded URLs when the router supports it.
- Use path params for resource identity and query params for optional, shareable view state such as filters or tabs.
- Validate route-derived values before using them in API calls or state transitions.
- Keep navigation decisions in pages or explicit navigation owners, not generic form/input components.
- Keep metadata and navigation entries synchronized when adding or removing a page.
- When embedded in a host application, respect the host's route ownership and clean up application resources on unmount. Do not assume every project is a microfrontend.

## Constants, configuration, and safety

- Extract repeated domain values, status codes, limits, and meaningful timing values into named constants.
- Do not extract every one-off literal into a distant constants file. An abstraction should improve understanding.
- Keep enum-like collections immutable when mutation is not part of their contract.
- Do not put constants into reactive state unless they need to be exposed there for a specific reason. Bare imports are valid inside script logic.
- Keep deployment-specific configuration outside feature logic. Do not hard-code environments, regions, or hostnames that vary by installation.
- Prefer authoritative capability data over scattered deployment assumptions when feature availability is dynamic.
- Treat client-side feature checks as UI behavior, not a substitute for server-side authorization.
- Never commit credentials or sensitive test data. Do not log secrets, access tokens, or sensitive form payloads.
- Avoid rendering untrusted HTML. Use established sanitization when HTML rendering is genuinely required.
- Confirm destructive actions with a clear description of the affected resource and consequence.
- Handle destructive-action loading and failure explicitly; refresh or remove affected data only according to the confirmed result.
