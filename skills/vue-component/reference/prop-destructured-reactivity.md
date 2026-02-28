# Destructured Props Lose Reactivity

**Impact**: HIGH — Component silently stops updating, extremely hard to debug.

## Problem

AI agents frequently destructure props and pass them to `watch` or `computed`.
Before Vue 3.5, destructured props are plain values — they lose reactivity.
Vue 3.5+ added reactive props destructure, but the agent must know which
version the project uses.

## ❌ Incorrect

```vue
<script setup lang="ts">
const props = defineProps<{ query: string }>()

// Destructured — loses reactivity in < 3.5
const { query } = props

// This watcher NEVER fires after initial render
watch(query, (val) => {
  fetchResults(val)
})

// This computed returns a stale value
const upper = computed(() => query.toUpperCase())
</script>
```

## ✅ Correct (works in all Vue 3 versions)

```vue
<script setup lang="ts">
const props = defineProps<{ query: string }>()

// Use getter function to preserve reactivity
watch(() => props.query, (val) => {
  fetchResults(val)
})

// Access via props.xxx in computed
const upper = computed(() => props.query.toUpperCase())
</script>
```

## ✅ Also correct (Vue 3.5+ only)

```vue
<script setup lang="ts">
// Reactive destructure — compiler rewrites to props.query automatically
const { query } = defineProps<{ query: string }>()

// Now this works because the compiler transforms it
watch(() => query, (val) => {
  fetchResults(val)
})
</script>
```

## Checklist

- [ ] If project is < Vue 3.5: NEVER destructure props, always use `props.xxx`
- [ ] If project is >= Vue 3.5: destructure directly from `defineProps()`, not from a `props` variable
- [ ] In `watch()`, always use a getter `() => props.xxx` for prop sources
- [ ] In `computed()`, access props via `props.xxx` or the 3.5+ destructured variable
