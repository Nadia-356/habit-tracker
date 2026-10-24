<script setup>
import { ref } from 'vue'

const props = defineProps({
  habit: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['toggle', 'delete', 'edit'])

const isEditing = ref(false)
const editedName = ref('')

function startEditing() {
  editedName.value = props.habit.name
  isEditing.value = true
}

function saveEdit() {
  const name = editedName.value.trim()

  if (name === '') {
    return
  }

  emit('edit', {
    id: props.habit.id,
    name: name
  })

  isEditing.value = false
}

function cancelEdit() {
  isEditing.value = false
}
</script>

<template>
  <div
    class="habit-card"
    :class="{ completed: habit.done }"
  >
    <template v-if="isEditing">
      <form @submit.prevent="saveEdit" class="edit-form">
        <input
          v-model="editedName"
          aria-label="Gewohnheit bearbeiten"
        />

        <button type="submit">
          💾 Speichern
        </button>

        <button type="button" class="btn-delete" @click="cancelEdit">
          ❌ Abbrechen
        </button>
      </form>
    </template>

    <template v-else>
      <h2>
        {{ habit.emoji }}
        <span :class="{ 'habit-done': habit.done }">
          {{ habit.name }}
        </span>
      </h2>

      <p>
        {{ habit.done ? 'Erledigt! 🎉' : 'Noch offen' }}
      </p>

      <div class="button-group">
        <button
            class="btn-complete"
            @click="emit('toggle', habit.id)"
        >
            {{ habit.done ? '↩️ Rückgängig' : '✅ Erledigt' }}
        </button>

        <button
            class="btn-edit"
            @click="startEditing"
        >
            ✏️ Bearbeiten
        </button>

        <button
            class="btn-delete"
            @click="emit('delete', habit.id)"
        >
            🗑️ Löschen
        </button>
        </div>
    </template>
  </div>
</template>

<style scoped>
.habit-card {
  background: var(--color-surface);
  border: 1px solid #e2e9e2;
  border-radius: 16px;
  padding: 20px;
  margin-bottom: 14px;
  transition: background 0.2s ease,
              transform 0.2s ease;
}

.habit-card:hover {
  transform: translateY(-2px);
}

.habit-card.completed {
  background: var(--color-primary-light);
}

.habit-done {
  text-decoration: line-through;
  color: #718273;
}

.edit-form {
  display: flex;
  flex-wrap: wrap;
  column-gap: 12px;
  row-gap: 20px;
}

.edit-form input {
  flex: 1 1 100%;
  min-width: 0;
  padding: 12px;
  border: 1px solid #cbd8ce;
  border-radius: 10px;
  background: white;
  color: var(--color-primary-dark);
}

.edit-form input:focus {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.btn-complete {
  background: #527c60;
  color: white;
}

.btn-edit {
  background: #e4ece5;
  color: #304d3b;
}

.btn-delete {
  background: #f5e2df;
  color: #a34d43;
}

.btn-complete:hover {
  background: #304d3b;
}

.btn-edit:hover {
  background: #d2e0d5;
  color: #304d3b;
}

.btn-delete:hover {
  background: #edc9c3;
  color: #8c382f;
}

h2 {
  color: var(--color-primary-dark);
  font-size: 1.1rem;
  overflow-wrap: anywhere;
}

button {
  background: var(--color-primary);
  color: white;
  border: none;
  border-radius: 8px;
  padding: 10px 14px;
  margin: 4px 8px 4px 0;
}

button:hover {
  background: var(--color-primary-dark);
}

.button-group {
  display: flex;
  gap: 12px;
  justify-content: flex-start;
  flex-wrap: wrap;
  margin-top: 20px;
}

.button-group button {
  margin: 0;
}

@media (max-width: 600px) {
  .habit-card {
    padding: 16px;
  }
}

@media (max-width: 480px) {
  .button-group {
    flex-direction: column;
  }

  .button-group button {
    width: 100%;
  }
}

@media (prefers-reduced-motion: reduce) {
  .habit-card {
    transition: none;
  }
}
</style>