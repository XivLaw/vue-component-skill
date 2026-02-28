# Smart vs Dumb Component Separation

**Impact**: MEDIUM — Affects testability, reusability, and maintainability.

## Problem

AI agents mix data fetching, business logic, and presentation in a single
component. This makes the component impossible to reuse in different contexts
or test without mocking APIs.

## ❌ Incorrect — everything in one component

```vue
<!-- ProductCard.vue — fetches its own data AND renders UI -->
<script setup lang="ts">
const props = defineProps<{ productId: number }>()
const product = ref(null)
const isInCart = ref(false)

// Smart logic embedded in presentational component
onMounted(async () => {
  product.value = await fetch(`/api/products/${props.productId}`).then(r => r.json())
  isInCart.value = await checkCart(props.productId)
})

async function addToCart() {
  await fetch('/api/cart', { method: 'POST', body: JSON.stringify({ id: props.productId }) })
  isInCart.value = true
}
</script>

<template>
  <div v-if="product" class="card">
    <h3>{{ product.name }}</h3>
    <p>{{ product.price }}</p>
    <button @click="addToCart">{{ isInCart ? 'In Cart' : 'Add' }}</button>
  </div>
</template>
```

## ✅ Correct — separate data layer from UI

```vue
<!-- ProductCard.vue — DUMB: pure presentation -->
<script setup lang="ts">
interface Props {
  name: string
  price: number
  isInCart: boolean
}
defineProps<Props>()
const emit = defineEmits<{ addToCart: [] }>()
</script>

<template>
  <div class="card">
    <h3>{{ name }}</h3>
    <p>{{ price }}</p>
    <button @click="emit('addToCart')">
      {{ isInCart ? 'In Cart' : 'Add' }}
    </button>
  </div>
</template>
```

```vue
<!-- ProductCardProvider.vue — SMART: handles data -->
<script setup lang="ts">
import { useProduct } from '@/composables/useProduct'
import ProductCard from './ProductCard.vue'

const props = defineProps<{ productId: number }>()
const { product, isInCart, addToCart } = useProduct(props.productId)
</script>

<template>
  <ProductCard
    v-if="product"
    :name="product.name"
    :price="product.price"
    :is-in-cart="isInCart"
    @add-to-cart="addToCart"
  />
</template>
```

## Checklist

- [ ] Dumb components: only props + emits, zero API calls, zero store access
- [ ] Smart components (or Provider wrappers): handle data, pass to dumb children
- [ ] Composables: extract shared stateful logic (useProduct, useAuth, etc.)
- [ ] Dumb components are testable with just props, no mocking needed
