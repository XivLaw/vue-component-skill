# Never Use v-if with v-for on the Same Element

**Impact**: MEDIUM — Performance waste, and in Vue 3 it behaves differently than Vue 2.

## Problem

In Vue 3, `v-if` has higher priority than `v-for` (opposite of Vue 2).
AI agents trained on mixed data sometimes place them on the same element,
which either errors (v-if can't access v-for variables) or wastes cycles.

## ❌ Incorrect

```vue
<template>
  <!-- Vue 3: v-if evaluates FIRST, so `item` is undefined → error -->
  <li v-for="item in items" v-if="item.isActive" :key="item.id">
    {{ item.name }}
  </li>
</template>
```

## ✅ Correct — use computed for filtering

```vue
<script setup lang="ts">
const activeItems = computed(() => items.value.filter(item => item.isActive))
</script>

<template>
  <li v-for="item in activeItems" :key="item.id">
    {{ item.name }}
  </li>
</template>
```

## ✅ Also correct — use template wrapper for conditional groups

```vue
<template>
  <template v-for="item in items" :key="item.id">
    <li v-if="item.isActive">{{ item.name }}</li>
  </template>
</template>
```

## Checklist

- [ ] Never put v-if and v-for on the same element
- [ ] Prefer computed property to pre-filter the list
- [ ] If filtering per-item is needed, wrap with `<template v-for>` + inner `v-if`
