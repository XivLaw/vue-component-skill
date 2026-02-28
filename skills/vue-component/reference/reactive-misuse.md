# reactive() Misuse Patterns

**Impact**: HIGH — Causes silent reactivity loss, state stops updating.

## Problem

AI agents misuse `reactive()` in two common ways: using it for primitives
(where it has no effect) and destructuring reactive objects (which breaks
reactivity). The general rule is: prefer `ref()` for everything, use
`reactive()` only when you have a strong reason.

## ❌ Incorrect — reactive with primitives

```vue
<script setup lang="ts">
import { reactive } from 'vue'

// WRONG: reactive() does nothing with primitives
const count = reactive(0)        // Not reactive!
const name = reactive('Alice')   // Not reactive!
</script>
```

## ❌ Incorrect — destructuring reactive

```vue
<script setup lang="ts">
import { reactive } from 'vue'

const state = reactive({ count: 0, name: 'Alice' })

// WRONG: destructured values are plain, non-reactive copies
const { count, name } = state
count++  // Does NOT update state.count
</script>
```

## ❌ Incorrect — reassigning reactive

```vue
<script setup lang="ts">
import { reactive } from 'vue'

let state = reactive({ items: [] })

// WRONG: reassignment breaks the reactive proxy
state = reactive({ items: newItems })
</script>
```

## ✅ Correct — use ref for most cases

```vue
<script setup lang="ts">
import { ref } from 'vue'

const count = ref(0)
const name = ref('Alice')
const items = ref<string[]>([])

// Reassignment works fine with ref
items.value = newItems
</script>
```

## ✅ Correct — if using reactive, never destructure or reassign

```vue
<script setup lang="ts">
import { reactive, toRefs } from 'vue'

const state = reactive({ count: 0, name: 'Alice' })

// Use toRefs if you need destructured reactive values
const { count, name } = toRefs(state)
count.value++  // ✅ Now this updates state.count

// Mutate properties, never reassign the object itself
state.count = 5  // ✅
</script>
```

## Checklist

- [ ] Default to `ref()` for all state — primitives AND objects
- [ ] Never use `reactive()` for primitive values
- [ ] Never destructure `reactive()` objects without `toRefs()`
- [ ] Never reassign a `reactive()` variable — mutate its properties instead
