# Do Not Use .value in Templates

**Impact**: MEDIUM — Causes runtime errors or accesses the wrong value.

## Problem

AI agents that understand `ref()` needs `.value` in scripts sometimes
over-apply the rule and add `.value` in templates. Vue automatically
unwraps refs in templates — adding `.value` either errors or accesses
a non-existent property.

## ❌ Incorrect

```vue
<script setup lang="ts">
import { ref } from 'vue'
const count = ref(0)
const user = ref({ name: 'Alice' })
</script>

<template>
  <!-- WRONG: .value in template -->
  <p>{{ count.value }}</p>
  <p>{{ user.value.name }}</p>
  <button @click="count.value++">Add</button>
</template>
```

## ✅ Correct

```vue
<script setup lang="ts">
import { ref } from 'vue'
const count = ref(0)
const user = ref({ name: 'Alice' })
</script>

<template>
  <!-- Refs auto-unwrap in templates -->
  <p>{{ count }}</p>
  <p>{{ user.name }}</p>
  <button @click="count++">Add</button>
</template>
```

## Exception — Refs in Scripts

`.value` IS required in `<script setup>`:

```vue
<script setup lang="ts">
const count = ref(0)
count.value++              // ✅ Required in script
console.log(count.value)   // ✅ Required in script
</script>
```

## Checklist

- [ ] Never use `.value` on refs in `<template>` — Vue auto-unwraps
- [ ] Always use `.value` on refs in `<script setup>`
- [ ] Watch for this when AI generates template expressions
