<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue'

let newTask = ref('')
let id = ref(0)
let viewAll = ref(true)

const tasks = ref([])
console.log(localStorage.getItem('tasks'))

onMounted(() => {
  tasks.value = localStorage.getItem('tasks') ? JSON.parse(localStorage.getItem('tasks')) : []
})
watch(tasks, () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
})
const filteredTasks = computed(() => {
  return (
    viewAll.value
      ? tasks.value
      : tasks.value.filter((task) => !task.done)
  )
})

const deleteTask = (id: number) => {
  tasks.value = tasks.value.filter((task) => task.id !== id)
}
const addTask = () => {
  if (!newTask.value) return
  tasks.value.push({
    id: id.value++,
    title: newTask.value,
    done: false,
  })
  newTask.value = ''
  id.value++
}
const deleteCompleteTasks = () => {
  tasks.value = tasks.value.filter((task) => !task.done)
}

</script>

<template>
  <main>
    <div class="wrapper">

      <h1>My Mini Todo App !!</h1>
      <div class="container-input">
        <input type="text" v-model="newTask" placeholder="Add a new task" @keydown.enter="addTask" />
        <button @click="addTask">Add</button>
      </div>
      <div v-if="tasks.length">
        <div class="container-btn">
          <button @click="viewAll = !viewAll">{{ viewAll ? 'Show only unfinished' : 'Show all' }}</button>
          <button @click="deleteCompleteTasks()">Clear completed</button>
        </div>
        <ul>
          <li v-for="task in filteredTasks" :key="task.id">
            <input type="checkbox" :checked="task.done" @change="task.done = !task.done" />
            <span :class="{ checked: task.done }">{{ task.title }}</span>
            <button @click="deleteTask(task.id)">❌</button>
          </li>
        </ul>
      </div>
      <div v-else>
        <h2>No tasks yet</h2>
      </div>

    </div>
  </main>
</template>

<style scoped>
main {
  width: 100%;
  height: 100vh;
  padding-top: 20rem;
}
.wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  margin: auto;
  width: 300px;
}

ul li {
  font-size: large;
  display: flex;
  align-items: center;
  gap: 1rem;
  width: 200%;
}
ul li button {
  background: none;
  border: none;
  cursor: pointer;
}
.checked {
  text-decoration: line-through;
}

.container-input {
  display: flex;
  justify-content: space-between;
  width: 100%;
}
.container-input input {
  font-size: medium;
  padding: 5px;
  width: 100%;
}
.container-btn {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1rem;
  width: 100%;
  margin-bottom: 20px;
}

</style>
