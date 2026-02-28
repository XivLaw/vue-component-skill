# v-for :key Must Use Stable Unique Identifiers

**Impact**: MEDIUM — Causes animation glitches, input state loss, wrong element updates.

## Problem

AI agents frequently use the array index as `:key`. When the list is
reordered, filtered, or items are added/removed, Vue cannot correctly
track which element is which — causing stale state, broken transitions,
and lost form inputs.

## ❌ Incorrect

```vue
<template>
  <!-- WRONG: index changes when list is reordered/filtered -->
  <div v-for="(user, index) in users" :key="index">
    <input v-model="user.name" />
  </div>
</template>
```

## ✅ Correct

```vue
<template>
  <!-- Use a stable unique ID from the data -->
  <div v-for="user in users" :key="user.id">
    <input v-model="user.name" />
  </div>
</template>
```

## When Index Is Acceptable

Only if ALL of these are true:
- The list is purely static (never reordered, filtered, or modified)
- Items have no internal state (no inputs, no animations)
- Items have no unique identifier available

In practice, this is almost never the case.

## Checklist

- [ ] Always use a stable unique ID from the data (e.g., `item.id`, `item.uuid`)
- [ ] Never use array index as `:key` for dynamic lists
- [ ] If data has no ID, generate one at creation time (`crypto.randomUUID()`)
