# Accessibility Essentials for Vue Components

**Impact**: MEDIUM — Excludes keyboard and screen reader users.

## Problem

AI agents generate visually correct components but miss keyboard navigation,
ARIA attributes, and semantic HTML. Common mistakes: clickable `<div>` instead
of `<button>`, missing labels on icon-only buttons, no focus management.

## ❌ Incorrect

```vue
<template>
  <!-- WRONG: div is not focusable or keyboard-activatable -->
  <div class="btn" @click="handleClick">
    <svg><!-- icon --></svg>
  </div>

  <!-- WRONG: no label for screen readers -->
  <button @click="toggleMenu">
    <svg><!-- hamburger icon --></svg>
  </button>

  <!-- WRONG: custom dropdown with no keyboard support -->
  <div class="dropdown" @click="open = !open">
    <div v-if="open">
      <div v-for="opt in options" @click="select(opt)">{{ opt }}</div>
    </div>
  </div>
</template>
```

## ✅ Correct

```vue
<template>
  <!-- Use semantic <button> — focusable + keyboard-activatable by default -->
  <button type="button" class="btn" @click="handleClick">
    <svg aria-hidden="true"><!-- icon --></svg>
  </button>

  <!-- Icon-only buttons need aria-label -->
  <button type="button" aria-label="Toggle menu" @click="toggleMenu">
    <svg aria-hidden="true"><!-- hamburger icon --></svg>
  </button>

  <!-- Dropdown with keyboard and ARIA support -->
  <div class="dropdown">
    <button
      type="button"
      aria-haspopup="listbox"
      :aria-expanded="open"
      @click="open = !open"
      @keydown.escape="open = false"
    >
      {{ selected }}
    </button>
    <ul v-if="open" role="listbox" @keydown.escape="open = false">
      <li
        v-for="opt in options"
        :key="opt"
        role="option"
        tabindex="0"
        :aria-selected="opt === selected"
        @click="select(opt)"
        @keydown.enter="select(opt)"
        @keydown.space.prevent="select(opt)"
      >
        {{ opt }}
      </li>
    </ul>
  </div>
</template>
```

## Checklist

- [ ] Use `<button>` for clickable actions, `<a>` for navigation — never `<div>`/`<span>`
- [ ] Icon-only buttons must have `aria-label`
- [ ] Decorative icons must have `aria-hidden="true"`
- [ ] Dropdowns/menus: `aria-haspopup`, `aria-expanded`, Escape to close
- [ ] Interactive lists: `role="listbox"` + `role="option"` + Enter/Space handlers
- [ ] Focus must be visible (never `outline: none` without replacement)
