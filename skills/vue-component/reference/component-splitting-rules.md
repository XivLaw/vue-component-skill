# Component Splitting Rules

**Impact**: HIGH — Mega-components are hard to test, maintain, and refactor.

## Problem

AI agents tend to generate 300-600 line single-file components that mix
data fetching, business logic, and presentation. This makes components
impossible to reuse or test in isolation.

## ❌ Incorrect — everything in one file

```vue
<!-- UserDashboard.vue — 500+ lines doing everything -->
<script setup lang="ts">
const users = ref([])
const searchQuery = ref('')
const selectedUser = ref(null)
const isEditing = ref(false)

// API calls mixed in
onMounted(async () => { users.value = await fetchUsers() })

// Business logic mixed in
function calculateStats(user) { /* 50 lines */ }
function validateForm(data) { /* 30 lines */ }

// 20 more functions...
</script>

<template>
  <!-- 200 lines of mixed search, list, detail, edit form -->
</template>
```

## ✅ Correct — split by responsibility

```
components/
  users/
    UserDashboard.vue          # Smart: wires data to children
    UserSearchBar.vue           # Dumb: props in, emit out
    UserList.vue                # Dumb: displays list
    UserDetailCard.vue          # Dumb: displays one user
    UserEditForm.vue            # Dumb: form with validation
composables/
  useUsers.ts                   # Data fetching + state
  useUserStats.ts               # Derived calculations
```

```vue
<!-- UserDashboard.vue — thin orchestrator -->
<script setup lang="ts">
import { useUsers } from '@/composables/useUsers'
import UserSearchBar from './UserSearchBar.vue'
import UserList from './UserList.vue'
import UserDetailCard from './UserDetailCard.vue'

const { users, searchQuery, filteredUsers, selectedUser } = useUsers()
</script>

<template>
  <UserSearchBar v-model="searchQuery" />
  <UserList :users="filteredUsers" @select="selectedUser = $event" />
  <UserDetailCard v-if="selectedUser" :user="selectedUser" />
</template>
```

```ts
// composables/useUsers.ts
export function useUsers() {
  const users = ref<User[]>([])
  const searchQuery = ref('')
  const selectedUser = ref<User | null>(null)

  const filteredUsers = computed(() =>
    users.value.filter(u =>
      u.name.toLowerCase().includes(searchQuery.value.toLowerCase())
    )
  )

  onMounted(async () => {
    users.value = await fetchUsers()
  })

  return { users, searchQuery, filteredUsers, selectedUser }
}
```

## When to Split

Split the component if ANY of these is true:
- It has more than one clear responsibility (data + UI + form + ...)
- It has 3+ distinct UI sections (list, detail, filters, footer)
- A template block is repeated or could become reusable
- The `<script setup>` exceeds ~100 lines of logic
- It fetches data AND renders complex UI

## Checklist

- [ ] Smart (container) components: fetch data, manage state, orchestrate children
- [ ] Dumb (presentational) components: receive props, emit events, render UI
- [ ] Extract reusable logic into `composables/useXxx.ts`
- [ ] Each component's purpose describable in one sentence
- [ ] Page/route components are thin orchestrators, not mega-components
