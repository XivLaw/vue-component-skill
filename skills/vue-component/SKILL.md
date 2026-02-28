---
name: vue-component
description: >
  MUST be used when creating, refactoring, or reviewing Vue 3 single-file
  components (.vue files). Covers Composition API with <script setup>,
  TypeScript typing, reactivity pitfalls, component splitting, and
  data flow patterns. Trigger on: "Vue component", "SFC", ".vue file",
  "script setup", "defineProps", "defineEmits", "defineModel", "v-model
  on component", "composable", or any task involving creating/editing
  *.vue files. Also trigger when converting React/Angular components to
  Vue or migrating Vue 2 components to Vue 3.
metadata:
  author: XivLaw
  version: "1.0.0"
---

> Based on Vue 3.5+. Only covers patterns AI agents frequently get wrong.

## Before You Start

1. Read the user's request and determine: standalone component, page component, or component library piece?
2. Default to `<script setup lang="ts">`. Only use Options API if the project explicitly requires it.
3. For non-trivial features, plan the component tree BEFORE writing code — define each component's single responsibility in one sentence.

## Component Structure

ALWAYS use this SFC section order: `<script setup>` → `<template>` → `<style scoped>`.

Within `<script setup>`, follow this declaration order:
1. Imports → 2. Props/Emits/Model → 3. State (ref/reactive) → 4. Computed → 5. Watchers → 6. Lifecycle → 7. Methods → 8. Expose

## Gotchas AI Gets Wrong

### Props & Emits
- Destructured props lose reactivity in watchers/computed → See [prop-destructured-reactivity](reference/prop-destructured-reactivity.md)
- Using wrong defineEmits syntax causing type errors → See [emit-type-based-syntax](reference/emit-type-based-syntax.md)
- v-model on custom components broken in Vue 3 → See [v-model-component-breaking-change](reference/v-model-component-breaking-change.md)

### Reactivity
- Calling `.value` in templates (unnecessary, causes errors) → See [ref-no-value-in-template](reference/ref-no-value-in-template.md)
- Using reactive() for primitives or destructuring reactive objects → See [reactive-misuse](reference/reactive-misuse.md)
- Computed with side effects or API calls → See [computed-no-side-effects](reference/computed-no-side-effects.md)

### Component Design
- 400+ line mega-components instead of proper splitting → See [component-splitting-rules](reference/component-splitting-rules.md)
- Using component refs to access child state instead of props/emits → See [prefer-props-emit-over-refs](reference/prefer-props-emit-over-refs.md)
- Mixing smart logic and presentational UI in one component → See [smart-dumb-separation](reference/smart-dumb-separation.md)

### Template
- v-if and v-for on the same element → See [no-v-if-with-v-for](reference/no-v-if-with-v-for.md)
- Using array index as :key in v-for → See [v-for-key-must-be-stable](reference/v-for-key-must-be-stable.md)

### Style & Accessibility
- Forgetting scoped on styles causing global leakage → See [style-must-be-scoped](reference/style-must-be-scoped.md)
- Missing keyboard/ARIA support on interactive elements → See [accessibility-essentials](reference/accessibility-essentials.md)
