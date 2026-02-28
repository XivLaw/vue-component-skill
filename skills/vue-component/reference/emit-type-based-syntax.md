# defineEmits Type-Based Syntax

**Impact**: MEDIUM — Causes TypeScript errors or loses type safety.

## Problem

AI agents often use the runtime declaration syntax for `defineEmits`, or mix
Vue 2 `$emit` patterns with Composition API. The type-based syntax is the
recommended approach in `<script setup lang="ts">`.

## ❌ Incorrect

```vue
<script setup lang="ts">
// Runtime syntax — works but loses precise payload typing
const emit = defineEmits(['update', 'delete', 'submit'])

// Wrong: Vue 2 style object syntax
const emit = defineEmits({
  update: (value: string) => true,
  delete: (id: number) => typeof id === 'number',
})
</script>
```

## ✅ Correct

```vue
<script setup lang="ts">
// Type-based — full TypeScript inference on emit payloads
const emit = defineEmits<{
  update: [value: string]
  delete: [id: number]
  submit: []
}>()

// Usage — TypeScript enforces correct payload
emit('update', 'new value')  // ✅
emit('update', 123)          // ❌ TS error: number not assignable to string
emit('submit')               // ✅
</script>
```

## Checklist

- [ ] Use `defineEmits<{}>()` type-based syntax, not runtime array or object
- [ ] Use labeled tuple syntax `[label: Type]` for each event's payload
- [ ] Use `[]` (empty tuple) for events with no payload
- [ ] Never import or use `this.$emit` in `<script setup>`
