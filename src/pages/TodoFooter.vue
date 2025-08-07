<template>
  <div class="bg-grey-2 text-dark q-pa-sm" v-show="todos.length > 0">
    <div class="row items-center justify-between full-width">
      <!-- Remaining count -->
      <div class="text-subtitle2">
        <strong>{{ remaining }}</strong>
        {{ remaining === 1 ? 'item' : 'items' }} left
      </div>

      <!-- Filters -->
      <q-tabs
        inline-label
        shrink
        class="text-primary"
        v-model="selectedTab"
      >
        <q-tab name="all" label="All" @click="goTo('all')" />
        <q-tab name="active" label="Active" @click="goTo('active')" />
        <q-tab name="completed" label="Completed" @click="goTo('completed')" />
      </q-tabs>

      <!-- Clear completed -->
      <q-btn
        v-show="todos.some(todo => todo.completed)"
        flat
        dense
        color="negative"
        label="Clear Completed"
        @click="emit('delete-completed')"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps(['todos'])
const emit = defineEmits(['delete-completed', 'tab-change'])

const selectedTab = ref('all')  // 👈 local state for v-model

const remaining = computed(() => props.todos.filter(todo => !todo.completed).length)

function goTo(tab) {
  selectedTab.value = tab
  emit('tab-change', tab)
}
</script>