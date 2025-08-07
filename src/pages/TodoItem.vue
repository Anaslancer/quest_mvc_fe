<template>
  <q-item
    :class="{ 'bg-grey-2': editing, 'text-strike': todo.completed }"
    clickable
    v-ripple
  >
    <!-- Checkbox -->
    <q-item-section avatar>
      <q-checkbox v-model="toggleModel" color="primary" />
    </q-item-section>

    <!-- Title (or Input when editing) -->
    <q-item-section>
      <div v-if="!editing" @dblclick="startEdit">
        {{ todo.title }}
      </div>

      <q-input
        v-else
        ref="editInput"
        v-model="editModel"
        dense
        outlined
        autofocus
        @keyup.enter="finishEdit"
        @blur="cancelEdit"
      />
    </q-item-section>

    <!-- Delete button -->
    <q-item-section side>
      <q-btn
        flat
        dense
        round
        color="negative"
        icon="delete"
        @click.stop="deleteTodo"
      />
    </q-item-section>
  </q-item>
</template>

<script setup>
import { ref, nextTick, computed } from 'vue'

const props = defineProps(['todo', 'index'])
const emit = defineEmits(['delete-todo', 'edit-todo', 'toggle-todo'])

const editing = ref(false)
const editInput = ref(null)
const editText = ref('')

const editModel = computed({
  get() {
    return props.todo.title
  },
  set(value) {
    editText.value = value
  },
})

const toggleModel = computed({
  get() {
    return props.todo.completed
  },
  set(value) {
    emit('toggle-todo', props.todo, value)
  },
})

function startEdit() {
  editing.value = true
  nextTick(() => {
    editInput.value.focus()
  })
}

function finishEdit() {
  editing.value = false
  if (editText.value.trim().length === 0) deleteTodo()
  else updateTodo()
}

function cancelEdit() {
  editing.value = false
}

function deleteTodo() {
  emit('delete-todo', props.todo)
}

function updateTodo() {
  emit('edit-todo', props.todo, editText.value)
  editText.value = ''
}
</script>