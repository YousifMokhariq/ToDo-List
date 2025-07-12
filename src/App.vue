<script setup></script>

<template>
  <div class="w-full mx-auto flex items-center justify-center bg-gray-800 p-5">
    <h1 class="text-white text-2xl font-bold">Tasks Menu</h1>
  </div>

  <!-- add task -->
  <div class="flex flex-row justify-center gap-6 mt-10">
    <input
      v-model="newTask"
      @keyup.enter="addTask"
      type="text"
      placeholder="Add a new task"
      class="px-4 py-2 border border-gray-300 rounded mt-5"
    />
    <button
      @click="addTask"
      class="px-7 py-2 bg-green-700 text-white rounded mt-5"
    >
      Add
    </button>
  </div>

  <!-- Tasks Filter -->
  <div class="flex justify-center items-center mt-10 gap-3">
    <button
      class="px-10 py-2 rounded-lg"
      :class="{
        'bg-blue-600 text-white': taskFilter === 'all',
        'bg-gray-200': taskFilter !== 'all',
      }"
      @click="taskFilter = 'all'"
    >
      All
    </button>
    <button
      class="px-10 py-2 rounded-lg"
      :class="{
        'bg-green-600 text-white': taskFilter === 'done',
        'bg-gray-200': taskFilter !== 'done',
      }"
      @click="taskFilter = 'done'"
    >
      Done
    </button>
    <button
      class="px-10 py-2 rounded-lg"
      :class="{
        'bg-yellow-600 text-white': taskFilter === 'not-done',
        'bg-gray-200': taskFilter !== 'not-done',
      }"
      @click="taskFilter = 'not-done'"
    >
      Not Done
    </button>
  </div>
  <!-- Task List -->
  <ul class="space-y-2 mt-10">
    <li
      v-for="task in filteredTasks"
      :key="task.text"
      class="flex items-center justify-between bg-gray-100 p-3 mx-12 rounded"
    >
      <div class="flex items-center gap-2">
        <input
          type="checkbox"
          :checked="task.done"
          @change="toggleDoneTask(task)"
        />
        <span :class="{ 'line-through text-gray-500': task.done }">
          {{ task.text }}
        </span>
      </div>
      <button
        @click="removeTask(index)"
        class="text-white bg-red-600 p-3 rounded hover:bg-red-700 transition duration-200"
      >
        Remove
      </button>
    </li>
  </ul>
</template>

<script setup>
import { ref, computed, watch, onMounted } from "vue";

const newTask = ref("");
const tasks = ref([]);
const taskFilter = ref("all");

function addTask() {
  const trimmedTask = newTask.value.trim();
  if (trimmedTask) {
    tasks.value.push({ text: trimmedTask, done: false });
    newTask.value = "";
  }
}

function removeTask(index) {
  tasks.value.splice(index, 1);
}

function toggleDoneTask(task) {
  task.done = !task.done;
}
const filteredTasks = computed(() => {
  if (taskFilter.value === "done") {
    return tasks.value.filter((task) => task.done);
  } else if (taskFilter.value === "not-done") {
    return tasks.value.filter((task) => !task.done);
  }
  return tasks.value;
});

onMounted(() => {
  const savedTasks = localStorage.getItem("tasks");
  if (savedTasks) {
    try {
      const parsedTasks = JSON.parse(savedTasks);
      if (Array.isArray(parsedTasks)) {
        tasks.value = parsedTasks;
      } else {
        console.warn("No Tasks found in localStorage.");
        tasks.value = [];
      }
    } catch (error) {
      console.error("Failed to load your tasks:", error);
      tasks.value = [];
    }
  } else {
    tasks.value = [];
  }
});

watch(
  tasks,
  (newTasks) => {
    localStorage.setItem("tasks", JSON.stringify(newTasks));
  },
  { deep: true }
);
</script>
