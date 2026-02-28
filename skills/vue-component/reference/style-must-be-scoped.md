# Styles Must Be Scoped

**Impact**: MEDIUM — Unscoped styles leak globally, breaking other components.

## Problem

AI agents sometimes omit `scoped` on `<style>` blocks, or use `<style>`
without any attribute. In multi-component applications, this causes global
CSS pollution.

## ❌ Incorrect

```vue
<!-- Styles leak to ALL components -->
<style>
.card { padding: 1rem; }
.title { font-size: 1.5rem; }
</style>
```

## ✅ Correct

```vue
<!-- Styles scoped to this component only -->
<style scoped>
.card { padding: 1rem; }
.title { font-size: 1.5rem; }
</style>
```

## Styling Child Components from Scoped Parent

Use `:deep()` selector when you need to override child styles:

```vue
<style scoped>
/* Only affects .inner inside child components rendered by this component */
.card :deep(.inner) {
  padding: 0.5rem;
}
</style>
```

## Exceptions

- Global reset/utility styles in `App.vue` or a dedicated `main.css`
- When using Tailwind/UnoCSS (styles are utility-class based, not scoped blocks)

## Checklist

- [ ] Every component `<style>` must have `scoped` attribute
- [ ] Use `:deep()` to style child component internals from parent
- [ ] Global styles only in app entry point, not in feature components
