# Pinia Best Practices

## Selection gate and scope

Use Pinia as the shared-state default for a greenfield Vue setup or a project
without a state manager when managed shared state is part of the requested work.
Preserve an existing manager, including Vuex, and an explicitly established
shared-state architecture. Do not add a parallel manager or migrate stores
without a request. An ordinary local-state feature or narrow fix is not an
infrastructure setup task.

Local refs, parent-owned props/events, feature-root providers, and independent
composables remain valid even in an application with Pinia. Installing Pinia is
not a reason to create empty stores or globalize every value. Choose ownership
using the state decision table before designing a store.

## Application setup

- Verify the Vue/Pinia versions and the framework's integration first. Use an
  existing Nuxt/SSR integration rather than registering a competing instance.
- In a client-rendered Vue app, create one Pinia instance for the app, register it
  before mounting, and before router installation if initial guards use stores.
- Call `useStore()` inside `setup()`, a composable called there, or a function
  invoked after registration. Do not instantiate a store at module import time.
- Outside an injection context, pass the correct app's Pinia instance explicitly
  when needed. Do not rely on a process-global active instance in SSR.
- For SSR, create Pinia per request and follow the framework's safe serialization
  and hydration contract. Never serialize secrets, private credentials, or
  server-only data into client state. Review state exposure rather than blindly
  returning sensitive values to satisfy a store convention.

Illustrative client-only startup, not a replacement for an existing entry point:

```js
import { createApp } from 'vue';
import { createPinia } from 'pinia';
import App from './App.vue';

const app = createApp(App);
const pinia = createPinia();

app.use(pinia);
// Register an existing router here, if the application uses one.
app.mount('#app');
```

Only introduce dependencies and edit startup/configuration/lockfiles as part of
an authorized setup. Do not install tools while reviewing or planning.

## Store design

- Use domain-oriented stores with unique IDs and names such as `useCartStore`.
  Avoid one enormous application store or a store for each small component.
- For a new store with no established convention, prefer an Option Store to
  align with this skill's Options API default. Preserve existing Setup Stores.
- Declare all state up front. Keep getters pure and derive values rather than
  synchronizing redundant totals and flags.
- Use explicit actions for domain transitions and async workflows. Although
  Pinia supports direct state mutation, actions make multi-step contracts clear.
- Keep transport details in the existing API layer. Model pending/error/empty
  outcomes and stale responses as needed; do not turn failures into empty success.
- Define reset rules for logout, account changes, and workflow completion.
  Option Stores provide `$reset()`; Setup Stores require their own reset action.
- In Setup Stores, return the state refs, computed getters, and actions Pinia
  needs to track. Hidden or readonly store state can break hydration, plugins,
  and devtools. This differs from the readonly boundary of a provided composable.
- Avoid circular store initialization: read another store in a getter/action when
  appropriate rather than having two stores eagerly read each other during setup.
- Timers, listeners, subscriptions, and requests need an explicit cleanup owner.
  A long-lived store does not cancel them merely because a page unmounts.

```js
// stores/cart.js — only when a cart really is shared across consumers.
import { defineStore } from 'pinia';

export const useCartStore = defineStore('cart', {
    state: () => ({
        itemIds: [],
    }),
    getters: {
        itemCount: (state) => state.itemIds.length,
    },
    actions: {
        addItem(id) {
            if (!this.itemIds.includes(id)) {
                this.itemIds.push(id);
            }
        },
        clearCart() {
            this.itemIds = [];
        },
    },
});
```

## Reactive consumption and Options API

Store properties are reactive through the store object. Plain destructuring of
state/getters loses their reactive connection; use `storeToRefs()` for those
properties. Actions are bound by Pinia and may be destructured directly.

```js
import { storeToRefs } from 'pinia';
import { useCartStore } from '../../stores/cart';

export default {
    name: 'CartSummary',
    setup() {
        const cart = useCartStore();
        const { itemIds, itemCount } = storeToRefs(cart);
        const { addItem, clearCart } = cart;

        return {
            itemIds,
            itemCount,
            addItem,
            clearCart,
        };
    },
};
```

The path is illustrative; verify it against the component's location. Returning
the whole store from `setup()` is also valid. When using Options API mapping
helpers, pass the definition: `mapStores(useCartStore)`, not
`mapStores(useCartStore())`. Use normal functions for actions that access `this`.

Do not apply generic method-binding warnings blindly to Pinia's bound actions.
In templates, pass domain arguments explicitly, for example
`@click="addItem(item.id)"`; a bare event handler may receive a DOM event instead
of the intended identifier. A no-argument bound action is fine as a handler.

## URL state, persistence, and tests

- Keep bookmarkable filters, tabs, and resource identity in validated route
  params/query when routing exists. Do not duplicate them as independent mutable
  store values. Define one synchronization direction at each boundary.
- A store alone does not survive refresh. Add persistence only for an explicit
  requirement with validation, expiration, migration, and reset rules. Do not
  persist sensitive authentication material by default.
- Test actions and derived state with the existing runner. Give each unit test a
  fresh Pinia instance; do not let state leak between tests or SSR requests.
- If using `createTestingPinia()`, remember actions are stubbed by default. Use
  real actions when the assertion concerns their behavior; do not claim action
  logic is tested when only calls were recorded.
- Verify reactive rendering, reset behavior, cross-route lifetime, failure paths,
  and cleanup relevant to the requested store. Do not install a test framework
  merely to satisfy this reference.

## Sources and adaptation

Adapted from [vue-pinia-best-practices](https://github.com/mrpvi/vue3-skills/tree/main/skills/vue-pinia-best-practices)
and its six references on active Pinia, Setup Store state, destructuring, method
binding, URL state, and managed application state. The source skill declares MIT.
This is a scoped synthesis, not a verbatim copy: broad migration recommendations
are excluded, and generic method-binding advice is distinguished from Pinia's
bound actions. Existing project conventions and the skill's AI scope rules win.

For version-specific details, consult the installed version and the official
[core concepts](https://pinia.vuejs.org/core-concepts/),
[outside-component usage](https://pinia.vuejs.org/core-concepts/outside-component-usage.html),
and [testing guide](https://pinia.vuejs.org/cookbook/testing.html).
The bundled guidance is self-contained; no network lookup is required to load it.
