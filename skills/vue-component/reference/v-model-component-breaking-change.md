# v-model on Custom Components — Vue 3 Breaking Change

**Impact**: HIGH — Component two-way binding silently fails.

## Problem

AI agents trained on mixed Vue 2/3 data frequently generate the Vue 2
`v-model` pattern (`value` prop + `input` event). Vue 3 changed this to
`modelValue` prop + `update:modelValue` event. Vue 3.4+ introduced
`defineModel()` which simplifies this further.

## ❌ Incorrect (Vue 2 pattern)

```vue
<!-- Parent -->
<MyInput v-model="name" />

<!-- MyInput.vue — WRONG: Vue 2 conventions -->
<script setup lang="ts">
defineProps<{ value: string }>()
const emit = defineEmits<{ input: [value: string] }>()
</script>

<template>
  <input :value="value" @input="emit('input', $event.target.value)" />
</template>
```

## ✅ Correct (Vue 3.0 - 3.3)

```vue
<!-- MyInput.vue -->
<script setup lang="ts">
defineProps<{ modelValue: string }>()
const emit = defineEmits<{ 'update:modelValue': [value: string] }>()
</script>

<template>
  <input
    :value="modelValue"
    @input="emit('update:modelValue', ($event.target as HTMLInputElement).value)"
  />
</template>
```

## ✅ Best (Vue 3.4+)

```vue
<!-- MyInput.vue -->
<script setup lang="ts">
const model = defineModel<string>({ required: true })
</script>

<template>
  <input v-model="model" />
</template>
```

## Named v-model (multiple v-models)

```vue
<!-- Parent -->
<UserForm v-model:first-name="first" v-model:last-name="last" />

<!-- UserForm.vue -->
<script setup lang="ts">
const firstName = defineModel<string>('firstName', { required: true })
const lastName = defineModel<string>('lastName', { required: true })
</script>
```

## Checklist

- [ ] Never use `value` + `input` event pattern (that is Vue 2)
- [ ] Use `modelValue` + `update:modelValue` for Vue 3.0-3.3 projects
- [ ] Use `defineModel()` for Vue 3.4+ projects (check project version first)
- [ ] For multiple v-models, use named `defineModel('name')` syntax
