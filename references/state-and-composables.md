# State and Composables

Adapted from Vue Contribution Rules, chapters 6, 7.
Apply these conventions only to the authorized task; they do not authorize unrelated cleanup.

## State ownership and data flow

- Keep state at the lowest level that can correctly own it.
- Keep form fields, open/closed flags, and page-only filters local unless another consumer or a longer lifetime requires sharing them.
- Use shared state for genuinely shared data or workflows, not simply because data came from an API.
- Maintain one source of truth. Avoid copying props, route state, or store state into independently mutable duplicates without a defined synchronization policy.
- Derive values instead of maintaining redundant flags and counters where possible.
- Prefer props down and events up for component communication.
- When using a store, follow its established action/update mechanism and keep UI-specific details out of shared domain state.
- A component may call a domain API operation directly for local data. Reuse an existing shared action when that action already owns the workflow.
- For greenfield setup or requested managed shared state without an existing manager, use Pinia and read [Pinia best practices](pinia-best-practices.md). Preserve an existing manager; do not add infrastructure during an unrelated fix.

### State-management decision table

Choose by **ownership, consumers, and lifetime**, not by the size of an object or whether it came from an API. `provide` / `inject` distributes an ancestor-owned dependency; it is not a separate persistence mechanism.

| Scenario | Choose | Ownership and lifetime | Example |
|---|---|---|---|
| Only one component needs the state | Component-local state | The component owns it; normally ends when that component unmounts | An open dropdown, input draft, or pending button state |
| A parent and its immediate children need the state | Parent-local state with props and events | The nearest common parent owns it; children receive values and emit changes | A page passes form values to a child and handles `update:*` events |
| A few siblings need the state | Lift state to their nearest common parent; use props/events | Shared only within that parent's lifetime | A filter panel and results list on one page |
| Many nested descendants need one feature's context, and intermediate components would only forward it | `provide` / `inject` | The feature root owns the state and provides a contract to descendants | A multi-step wizard's draft, validation context, or compound component's selection |
| Multiple independent feature instances must each have isolated shared state | One provider per feature instance | Each subtree gets its own context; no accidental global sharing | Two independent editors rendered on the same screen |
| Unrelated features need coordinated shared state and no manager exists | Pinia, when this is part of the requested setup | Application-owned domain store with explicit actions and reset policy | A new app needs a queue consumed by unrelated routes |
| Unrelated branches or routes must coordinate the same domain state | The existing managed store, or Pinia under the missing-manager setup gate | Explicit shared owner with domain actions and a defined reset policy | A cart used by product pages, header, and checkout |
| A workflow must continue after its initiating page unmounts | A managed store or established application-level owner | The application owns the workflow and its cleanup | An upload queue with progress shown on several pages |
| The same reactive behavior is needed, but each consumer needs independent values | A composable called separately by each consumer | Each invocation owns independent state | Two tables each call `useSelection()` and keep separate selections |
| State must be bookmarkable or shareable through a URL | Router params/query, with local derived state as needed | The URL owns the serializable view state | A resource ID, active tab, or list filter |
| State must survive a browser refresh | An explicit persistence policy, separate from the choice above | Define storage, validation, expiry, and restoration deliberately | A non-sensitive saved draft; a store alone does not provide persistence |

### Decision order

1. If only one component needs the state, keep it local.
2. If a parent and a small number of children or siblings need it, use the nearest common parent with props/events.
3. If it belongs to one nested feature subtree and prop forwarding obscures the code, use `provide` / `inject` at the feature root.
4. If unrelated features/routes need the same state, or the workflow must outlive its page, use the established managed store or application-level owner.
5. Consider URL state and persistence separately. Neither `provide` / `inject` nor a store automatically makes state durable.

### Provider and store contracts

- Keep mutations with the owner. A provider should expose read-only reactive state plus explicit actions, rather than allowing descendants to mutate injected objects arbitrarily.
- Use a shared `Symbol` injection key, imported by provider and consumers. When using TypeScript, an `InjectionKey` can type the contract.
- Provide refs, reactive objects, or computed values when updates must remain reactive. Providing a primitive snapshot does not make it reactive.
- Required injections must fail clearly when the provider is missing. Optional injections must have an intentional fallback.
- A child injects from an ancestor; siblings cannot inject directly from one another. Use their common ancestor as provider.
- A provider makes state available to descendants but does not itself cancel requests or clear timers. The owner still performs cleanup.
- Use store actions for coordinated domain updates. Define when shared data is reset, especially on logout or account/context changes.
- Do not place temporary UI state in a global store simply to avoid passing one prop.
- Do not turn every composable into a module-level singleton. Reusing behavior is different from sharing one state instance.
- If no manager exists and managed shared state is required by the requested feature or setup, select Pinia rather than inventing a new singleton-store system. If the needed setup exceeds an exclusive file list or approved scope, explain the prerequisite before editing. Preserve an explicitly established application-level ownership architecture; local refs and feature providers alone do not establish such an architecture.
- For server-rendered applications, create application state per request. Do not leak one user's state through module-level mutable singletons.

## Reusable logic and abstraction

- Extract a component when it has a coherent UI responsibility, a useful independent contract, or genuine reuse—not merely because a file passes an arbitrary line count.
- Extract a utility for framework-independent logic. Utilities should not secretly read Vue state, navigate, display notifications, or make network requests.
- Extract a composable for reusable reactive or lifecycle-aware logic. Prefix its name with `use` and make its side effects and cleanup clear.
- Keep feature-specific business decisions in the feature, even when a shared component renders the result.
- Prefer composition over introducing new mixins. Maintain established mixins where required without adding a competing implementation of the same helper.
- Avoid speculative configuration options and generic frameworks for a single use case.

### Composable example: independent selection state

This JavaScript example needs only Vue. It does not require TypeScript or a store. Place it inside a feature when only that feature needs it; promote it to shared `composables/` only when appropriate.

```js
// composables/useSelection.js
import { computed, readonly, ref } from 'vue';

export const useSelection = (initialIds = []) => {
    const selectedIds = ref([...new Set(initialIds)]);
    const selectedCount = computed(() => selectedIds.value.length);
    const hasSelection = computed(() => selectedCount.value > 0);

    const isSelected = (id) => selectedIds.value.includes(id);

    const toggleSelection = (id) => {
        if (isSelected(id)) {
            selectedIds.value = selectedIds.value.filter((selectedId) => selectedId !== id);
            return;
        }

        selectedIds.value = [...selectedIds.value, id];
    };

    const clearSelection = () => {
        selectedIds.value = [];
    };

    return {
        selectedIds: readonly(selectedIds),
        selectedCount,
        hasSelection,
        isSelected,
        toggleSelection,
        clearSelection,
    };
};
```

Consume it from an Options API component without moving unrelated component logic into Composition API:

```vue
<template>
    <section class="item-list">
        <ul class="item-list__items">
            <li
                v-for="item in items"
                :key="item.id"
                class="item-list__item"
            >
                <button
                    type="button"
                    class="item-list__selection"
                    :aria-pressed="isSelected(item.id)"
                    @click="toggleSelection(item.id)"
                >
                    {{ item.name }}
                </button>
            </li>
        </ul>
        <button
            type="button"
            class="item-list__clear"
            :disabled="!hasSelection"
            @click="clearSelection"
        >
            Clear selection ({{ selectedCount }})
        </button>
    </section>
</template>

<script>
import { useSelection } from '../../composables/useSelection';

export default {
    name: 'ItemList',
    props: {
        items: {
            type: Array,
            default: () => [],
        },
    },
    setup() {
        const {
            selectedCount,
            hasSelection,
            isSelected,
            toggleSelection,
            clearSelection,
        } = useSelection();

        return {
            selectedCount,
            hasSelection,
            isSelected,
            toggleSelection,
            clearSelection,
        };
    },
};
</script>
```

The relative import assumes `components/ItemList/index.vue` and the example source tree; adjust it to the actual location. Localize the button label when localization is supported.

**Why this follows the rules:**

- Every invocation creates its own selection state; two lists do not share selections accidentally.
- The composable returns refs/computed values and explicit actions. Destructuring the returned object preserves reactivity because its state values are refs, not primitive snapshots.
- Consumers cannot mutate the exposed selection array directly; changes go through actions.
- `initialIds` is an initialization snapshot, not a watched input. IDs should be stable primitive identifiers. The caller decides whether to clear selection when the dataset changes or preserve it across pagination.
- There are no timers, listeners, or network effects here, so no cleanup hook is needed. Composables that create such resources must register cleanup, for example with `onScopeDispose()` inside an active Vue scope.
- Call composables synchronously from `setup()` (or another composable invoked there) when they depend on component lifecycle, injection, or scope cleanup. Do not call lifecycle-dependent composables for the first time inside a later click handler.
- To share this selection within a subtree, call `useSelection()` once in its owner and provide the resulting contract. Calling it again in every descendant creates separate state, not shared state.
