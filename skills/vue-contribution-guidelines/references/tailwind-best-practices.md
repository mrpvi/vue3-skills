# Tailwind Best Practices

## Selection gate and scope

Use Tailwind for a greenfield Vue setup or requested styling in a project with
no styling method. Inspect actual styles, build configuration, and dependencies
before declaring the styling method absent. Plain CSS, SCSS, CSS Modules, scoped
SFC styles, and established component-library styling all count as existing
methods. Absence of a Tailwind dependency is not absence of a styling method.

Preserve existing styling. Do not introduce Tailwind alongside an established
approach or migrate existing classes unless requested. If Tailwind is already
used, follow its installed version and configuration. A small functional bug fix
is not permission to change dependencies, build plugins, or stylesheets.

Tailwind utilities are framework-provided class names, so they are exempt from
BEM. Custom application-authored CSS/SCSS classes still use BEM. Do not invent
BEM wrappers or rename utilities merely to satisfy the class-naming rule.

## Version and integration first

- Default new compatible setups to Tailwind v4. Check build tooling and browser
  support before selecting the integration. If requirements are incompatible,
  explain the constraint rather than silently changing the project's targets.
- Existing v3 projects remain v3 unless an upgrade is explicitly requested. Use
  their configuration and syntax; do not insert v4 examples into v3 stylesheets.
- For a new v4 Vite setup, use the supported Vite integration; for PostCSS use
  the v4 PostCSS integration. Do not assume the `tailwindcss` package itself is
  the v4 PostCSS plugin. Follow framework-specific integration where present.
- Import the main stylesheet once at the established application entry point.
  Inspect existing resets before adding Preflight; do not enable a global reset
  as a side effect of an unrelated embedded-widget change.
- Keep package, lockfile, entry, and build-config edits within the authorized
  setup. Do not install formatters, class-merging helpers, or plugins by default.

## New Tailwind v4 syntax

These examples apply to **v4**, not as an automatic migration checklist:

| Intent | Use in v4 | Do not generate for a new v4 setup |
|---|---|---|
| Main stylesheet entry | `@import "tailwindcss";` | v3 `@tailwind base/components/utilities` directives |
| Design tokens | CSS-first `@theme` | A new legacy `tailwind.config.js` by habit |
| Background/text opacity | `bg-red-500/50`, `text-black/75` | `bg-opacity-50`, `text-opacity-25` |
| Linear gradient | `bg-linear-to-r`, `bg-linear-45` | Legacy directional gradient naming |
| CSS variable color | `bg-(--brand-color)` | `bg-[--brand-color]` |
| Grid arbitrary values | `grid-cols-[max-content_auto]` | Comma-separated tracks for CSS spaces |
| Important utility, only if justified | `flex!` | Leading `!flex` in newly authored v4 code |
| Parent-size responsiveness | `@container` parent; `@md:grid-cols-2` child | Viewport breakpoints when the component depends on its container |

Verify utilities against the actual version rather than guessing class names.
Legacy configuration compatibility is not a reason to rewrite an existing setup
while implementing an unrelated feature. Prefer normal cascade and variants
over important modifiers in any version.

A minimal illustrative v4 application stylesheet:

```css
@import "tailwindcss";

@theme {
    --color-brand-600: #0369a1;
    --color-brand-700: #075985;
}
```

The token names and values are examples, not an instruction to replace existing
brand values. `@theme` defines tokens that generate utility APIs; use ordinary
CSS custom properties when a value should not create utilities. Keep semantic
custom selectors in BEM when CSS is clearer than a utility combination.

## Vue templates and class detection

- Write complete utility tokens in source. Do not construct strings such as
  `'bg-' + color + '-500'`; the build may not detect the resulting class.
- Use Vue's object or array `:class` bindings with complete strings for finite
  variants. Prefer existing reusable components over a generic styling framework.
- For truly dynamic values, use validated style bindings or CSS variables and a
  statically detectable utility. Do not interpolate untrusted CSS values.
- Verify that actual source files are scanned, especially in monorepos or shared
  packages. Add explicit source declarations only where the version/integration
  requires them; avoid broad safelists that hide incorrect detection.
- Use utilities in templates for normal layout/state styling. Avoid converting
  every combination into `@apply` or adding duplicate per-component stylesheets.
- When v4 SFC styles genuinely need `@apply` or theme-aware directives, use the
  appropriate `@reference` to the shared theme instead of importing the whole
  Tailwind stylesheet into every component.

Example inside an existing component with `isSubmitting` already defined:

```vue
<button
    type="submit"
    class="rounded px-4 py-2 font-medium focus-visible:outline-2 focus-visible:outline-offset-2"
    :class="{
        'bg-brand-600 text-white hover:bg-brand-700': !isSubmitting,
        'cursor-wait bg-slate-200 text-slate-600': isSubmitting,
    }"
    :disabled="isSubmitting"
>
    Save
</button>
```

This assumes the illustrated brand tokens exist. Keep the actual label/localized
copy and component behavior from the task. Test focus contrast against the real
background rather than assuming the example guarantees accessibility.

## Layout, interaction, and verification

- Start with narrow-screen styles; add viewport breakpoints when the viewport
  drives layout. Use container queries when available width within the parent
  drives it. Neither mechanism replaces the other universally.
- Cover keyboard focus, disabled state, hover where appropriate, reduced motion,
  and the application's existing dark-mode strategy. Utilities do not create
  semantic HTML, accessible labels, or contrast guarantees.
- Prefer logical spacing/alignment utilities for bidirectional layouts when
  supported by the project's version. Test RTL where the app supports it.
- Reuse existing theme tokens before one-off arbitrary values. Avoid conflicting
  utilities and unnecessary specificity; use existing class-order tooling only
  within changed code, not a repository-wide formatting pass.
- Run the existing CSS/application build to catch unavailable classes or broken
  integration. Check generated styles and render relevant interaction states at
  narrow and wide widths. A parsed template alone does not prove CSS generation.
- Report unexecuted visual/build checks plainly; do not claim that this reference
  or a package-link check proves runtime layout correctness.

## Sources and adaptation

Adapted from the requested
[Tailwind best-practices rules](https://github.com/ofershap/tailwind-best-practices/blob/main/rules/best-practices.mdc).
That source specifically targets v4. Its syntax guidance is scoped here to new
compatible v4 setups and existing v4 work, not imposed on v3 or non-Tailwind
projects. Selection gates, Vue integration notes, and scope safeguards are
additional guidance for this contribution skill, not claims copied from the source.

For version-specific integration and compatibility details, consult the installed
version and the official [installation guide](https://tailwindcss.com/docs/installation),
[upgrade guide](https://tailwindcss.com/docs/upgrade-guide), and
[source-detection guide](https://tailwindcss.com/docs/detecting-classes-in-source-files).
The bundled guidance is self-contained; no network lookup is required to load it.
