# Prefer Props/Emits Over Component Refs

**Impact**: MEDIUM — Creates tight coupling, breaks encapsulation.

## Problem

AI agents often use `ref()` + `defineExpose()` to let parents directly
call child methods or read child state. This creates invisible dependencies
and makes components hard to reuse independently.

## ❌ Incorrect

```vue
<!-- Parent.vue -->
<script setup lang="ts">
import { ref } from 'vue'
import ChildForm from './ChildForm.vue'

const formRef = ref<InstanceType<typeof ChildForm>>()

function handleSubmit() {
  // Tight coupling: parent reaches into child internals
  if (formRef.value?.validate()) {
    const data = formRef.value?.getFormData()
    submitToApi(data)
  }
}
</script>

<template>
  <ChildForm ref="formRef" />
  <button @click="handleSubmit">Submit</button>
</template>

<!-- ChildForm.vue -->
<script setup lang="ts">
function validate() { /* ... */ }
function getFormData() { /* ... */ }
defineExpose({ validate, getFormData })
</script>
```

## ✅ Correct — data flows through props and emits

```vue
<!-- Parent.vue -->
<script setup lang="ts">
import ChildForm from './ChildForm.vue'

function handleSubmit(data: FormData) {
  submitToApi(data)
}
</script>

<template>
  <ChildForm @submit="handleSubmit" />
</template>

<!-- ChildForm.vue -->
<script setup lang="ts">
const emit = defineEmits<{ submit: [data: FormData] }>()

function handleSubmit() {
  if (validate()) {
    emit('submit', getFormData())
  }
}
</script>

<template>
  <form @submit.prevent="handleSubmit">
    <!-- form fields -->
    <button type="submit">Submit</button>
  </form>
</template>
```

## When Refs ARE Acceptable

- Imperative DOM actions: `.focus()`, `.scrollIntoView()`
- Third-party library integration requiring DOM references
- NOT for data flow between components

## Checklist

- [ ] Data flows down via props, events flow up via emits
- [ ] Never use `defineExpose()` to share state — use props/emits instead
- [ ] `ref()` on components only for DOM-level imperative actions
