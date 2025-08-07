<template>
  <q-page class="todoapp">
    <h4 class="user-name">Hi, {{ authStore.user?.first_name }} {{ authStore.user?.last_name }}</h4>

    <!-- Header -->
    <TodoHeader @add-todo="addTodo" />

    <!-- Main Section -->
    <main class="main" v-show="todos.length > 0">
      <!-- Toggle All -->
      <div class="toggle-all-container row items-center">
        <q-checkbox
          v-model="toggleAllModel"
          :disable="filteredTodos.value.length === 0"
          color="primary"
          class="toggle-all"
        />
      </div>

      <!-- Todo List -->
      <q-list bordered separator class="todo-list">
        <TodoItem
          v-for="(todo, index) in filteredTodos.value"
          :key="todo.id"
          :todo="todo"
          :index="index"
          @delete-todo="deleteTodo"
          @edit-todo="editTodo"
          @toggle-todo="toggleTodo"
        />
      </q-list>
    </main>

    <!-- Footer -->
    <TodoFooter :todos="todos" @delete-completed="deleteCompleted" @tab-change="currentTab = $event" />
  </q-page>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useAuthStore } from 'stores/auth'

import TodoFooter from './TodoFooter.vue'
import TodoHeader from './TodoHeader.vue'
import TodoItem from './TodoItem.vue'

const authStore = useAuthStore()
const currentTab = ref('all')
const todos = ref([])

const filters = {
  all: (todos) => todos,
  active: (todos) => todos.value.filter((todo) => !todo.completed),
  completed: (todos) => todos.value.filter((todo) => todo.completed),
}

const activeTodos = computed(() => filters.active(todos))
const completedTodos = computed(() => filters.completed(todos))
const filteredTodos = computed(() => {
  switch (currentTab.value) {
    case 'active':
      return activeTodos
    case 'completed':
      return completedTodos
    default:
      return todos
  }
})

const toggleAllModel = computed({
  get() {
    return activeTodos.value.length === 0
  },
  set(value) {
    todos.value.forEach((todo) => {
      todo.completed = value
    })
  },
})

function uuid() {
  let uuid = ''
  for (let i = 0; i < 32; i++) {
    let random = (Math.random() * 16) | 0

    if (i === 8 || i === 12 || i === 16 || i === 20) uuid += '-'

    uuid += (
      i === 12 ? 4 : i === 16 ? (random & 3) | 8 : random
    ).toString(16)
  }
  return uuid
}

function addTodo(value) {
  todos.value.push({
    completed: false,
    title: value,
    id: uuid(),
  })
}

function deleteTodo(todo) {
  todos.value = todos.value.filter((t) => t !== todo)
}

function toggleTodo(todo, value) {
  todo.completed = value
}

function editTodo(todo, value) {
  todo.title = value
}

function deleteCompleted() {
  todos.value = todos.value.filter((todo) => !todo.completed)
}

</script>
