

<script setup>
import { ref, computed, watch } from 'vue'
import HabitCard from './components/HabitCard.vue'

const appName = ref('Habit Tracker')

const defaultHabits = [
  { id: 1, name: '20 Minuten programmieren', done: false, emoji:'💻' },
  { id: 2, name: 'Qi-Gong machen', done: false, emoji:'🧘' },
  { id: 3, name: 'Ein Buch lesen', done: false, emoji:'📖' },
  { id: 4, name: 'Trainieren gehen', done: false, emoji:'💪' }
]

const savedHabits = localStorage.getItem('habits')

const habits = ref(
  savedHabits !== null
    ? JSON.parse(savedHabits)
    : defaultHabits
)

const completedCount = computed(() => {
  return habits.value.filter(habit => habit.done).length
})

const progress = computed(() => {
  if (habits.value.length === 0) {
    return 0
  }

  return Math.round(
    (completedCount.value / habits.value.length) * 100
  )
})

const newHabit = ref('')

function addHabit() {
  const name = newHabit.value.trim()

  if (name === '') {
    return
  }

  const newId = Math.max(
    0,
    ...habits.value.map(h => h.id)
  ) + 1

  habits.value.push({
    id: newId,
    name: name,
    emoji: '🌿',
    done: false
  })

  newHabit.value = ''
}

watch(
  habits,
  (newHabits) => {
    localStorage.setItem(
      'habits',
      JSON.stringify(newHabits)
    )
  },
  { deep: true }
)

function deleteHabit(id) {
  habits.value = habits.value.filter(
    h => h.id !== id
  )
}

function toggleHabit(id) {
  const habit = habits.value.find(h => h.id === id)

  if (habit) {
    habit.done = !habit.done
  }
}

function editHabit({ id, name }) {
  const habit = habits.value.find(h => h.id === id)

  if (!habit) {
    return
  }

  habit.name = name
}

function resetHabits() {
  const confirmed = window.confirm(
    'Möchtest du wirklich alle Gewohnheiten zurücksetzen?'
  )

  if (!confirmed) {
    return
  }

  habits.value = defaultHabits.map(habit => ({
    ...habit
  }))
}

</script>

<template>
  <main class="container">
    <h1>🌱 {{ appName }}</h1>
    <p>Jeden Tag ein bisschen wachsen. 🌿</p>

    <div class="progress-card">
      <h2>Dein Tagesfortschritt</h2>

      <p>
        {{ completedCount }} von {{ habits.length }}
        Gewohnheiten erledigt
      </p>

      <div
        class="progress-bar"
        role="progressbar"
        :aria-valuenow="progress"
        aria-valuemin="0"
        aria-valuemax="100"
        aria-label="Tagesfortschritt"
      >
        <div
          class="progress-fill"
          :style="{ width: progress + '%' }"
        ></div>
      </div>

      <p>{{ progress }} % geschafft!</p>
      <p v-if="progress === 0">
        Jeder kleine Schritt zählt! 🌱
      </p>
      <p v-else-if="progress < 100">
        Weiter so! Du schaffst das! 💪
      </p>
      <p v-else-if="progress === 100">
        Fantastisch! Alle Gewohnheiten erledigt! 🎉
      </p>
    </div>

    <div v-if="habits.length === 0" class="empty-state">
      <div class="empty-icon">🌱</div>

      <h2>Dein Garten wartet auf dich!</h2>

      <p>
        Noch keine Gewohnheiten vorhanden.
        Füge deine erste Gewohnheit hinzu!
      </p>
    </div>

    <template v-else>
      <HabitCard
        v-for="habit in habits"
        :key="habit.id"
        :habit="habit"
        @toggle="toggleHabit"
        @delete="deleteHabit"
        @edit="editHabit"
      />
    </template>

    <form @submit.prevent="addHabit" class="add-habit">
      <input
        v-model="newHabit"
        type="text"
        placeholder="Neue Gewohnheit ..."
      />

      <button type="submit">
        + Hinzufügen
      </button>
    </form>
    <div>
      <button @click="resetHabits">
        🔄 Gewohnheiten zurücksetzen
      </button>
    </div>
  </main>
</template>


<style scoped>
.container {
  width: min(100% - 32px, 760px);
  margin: 48px auto;
}

.empty-state {
  text-align: center;
  padding: 48px 24px;
  background: white;
  border-radius: 20px;
  margin: 24px 0;
  border: 1px dashed #cbd8ce;
}

.empty-icon {
  font-size: 3rem;
  margin-bottom: 16px;
}

.empty-state h2 {
  color: var(--color-primary-dark);
  margin-bottom: 12px;
}

.empty-state p {
  color: var(--color-text-muted);
  line-height: 1.6;
}

h1 {
  color: var(--color-primary-dark);
  font-size: clamp(1.8rem, 5vw, 2.6rem);
  margin-bottom: 8px;
}

.progress-card {
  background: var(--color-surface);
  padding: 28px;
  border-radius: 20px;
  margin: 32px 0;
  box-shadow: 0 4px 20px rgba(48, 77, 59, 0.05);
}

.progress-bar {
  height: 12px;
  background: var(--color-primary-light);
  border-radius: 20px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: var(--color-primary);
  border-radius: inherit;
  transition: width 0.3s ease;
}

.add-habit {
  display: flex;
  gap: 12px;
  margin-top: 24px;
}

.add-habit input {
  flex: 1;
  min-width: 0;
  padding: 14px;
  border: 1px solid #cbd8ce;
  border-radius: 12px;
  background: white;
}

.add-habit button,
.container > div > button {
  background: var(--color-primary);
  color: white;
  border: none;
  border-radius: 12px;
  padding: 14px 18px;
}

.container > div > button {
  margin-top: 20px;
}

/* Smartphone */
@media (max-width: 600px) {
  .container {
    width: calc(100% - 24px);
    margin: 24px auto;
  }

  .progress-card {
    padding: 20px;
  }

  .add-habit {
    flex-direction: column;
  }

  .add-habit button {
    width: 100%;
  }
}
</style>