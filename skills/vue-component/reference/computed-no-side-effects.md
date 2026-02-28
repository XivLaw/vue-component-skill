# Computed Must Not Have Side Effects

**Impact**: HIGH — Causes unpredictable behavior, infinite loops, or stale data.

## Problem

AI agents often put API calls, DOM mutations, or state modifications inside
`computed()`. Computed properties should be pure derivations — they run
whenever dependencies change and Vue expects them to be side-effect free.

## ❌ Incorrect

```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

const userId = ref(1)

// WRONG: API call inside computed
const user = computed(async () => {
  const res = await fetch(`/api/users/${userId.value}`)
  return res.json()
})

// WRONG: mutating other state inside computed
const count = ref(0)
const doubled = computed(() => {
  count.value++  // Side effect! Causes infinite loop
  return count.value * 2
})
</script>
```

## ✅ Correct — use watch/watchEffect for side effects

```vue
<script setup lang="ts">
import { ref, computed, watchEffect } from 'vue'

const userId = ref(1)
const user = ref(null)

// Side effects go in watchEffect
watchEffect(async () => {
  const res = await fetch(`/api/users/${userId.value}`)
  user.value = await res.json()
})

// Computed stays pure
const count = ref(0)
const doubled = computed(() => count.value * 2)
</script>
```

## Checklist

- [ ] `computed()` must be a pure function — no fetch, no state mutation, no DOM access
- [ ] If you need to react to changes with side effects, use `watch()` or `watchEffect()`
- [ ] `computed()` must never be `async`
- [ ] If AI generates `computed(async () => ...)` — that is always wrong
