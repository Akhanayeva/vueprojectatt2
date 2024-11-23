<template>
  <div class="todo-app">
    <h1>To-Do List</h1>

    <div class="new-task">
      <input
        type="text"
        v-model="newTaskTitle"
        placeholder="Enter task title"
      />
      <select v-model="newTaskPriority">
        <option value="low">Low</option>
        <option value="medium">Medium</option>
        <option value="high">High</option>
      </select>
      <button @click="addTask">Add Task</button>
    </div>

    <transition-group name="task" tag="ul" class="task-list">
      <li
        v-for="task in sortedTasks"
        :key="task.id"
        :class="[{ completed: task.completed }, task.priority]"
      >
        <span @click="toggleTask(task.id)">{{ task.title }}</span>
        <button @click="deleteTask(task.id)">Delete</button>
      </li>
    </transition-group>
    <p>Total Pending Tasks: {{ pendingTasks }}</p>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, watch, nextTick } from 'vue';
import { Task } from '../types/Task';

export default defineComponent({
  name: 'TaskList',
  setup() {
    const tasks = ref<Task[]>([]);
    const newTaskTitle = ref('');
    const newTaskPriority = ref<Task['priority']>('low');

    // Computed properties
    // Computed properties
    const sortedTasks = computed(() => {
    return [...tasks.value].sort((a, b) => {
    const priorityOrder = { high: 3, medium: 2, low: 1 };
    return priorityOrder[b.priority] - priorityOrder[a.priority];
      });
    });


    const pendingTasks = computed(() => {
      return tasks.value.filter((task) => !task.completed).length;
    });

    // Methods
    const addTask = async () => {
      if (newTaskTitle.value.trim()) {
        const newTask: Task = {
          id: Date.now(),
          title: newTaskTitle.value,
          priority: newTaskPriority.value,
          completed: false,
        };
        tasks.value.push(newTask);
        newTaskTitle.value = '';
        newTaskPriority.value = 'low';

        // Scroll to newly added task
        await nextTick();
        const taskList = document.querySelector('.task-list');
        if (taskList) {
          taskList.scrollTop = taskList.scrollHeight;
        }
      }
    };

    const toggleTask = (id: number) => {
      const task = tasks.value.find((task) => task.id === id);
      if (task) task.completed = !task.completed;
    };

    const deleteTask = (id: number) => {
      tasks.value = tasks.value.filter((task) => task.id !== id);
    };

    // Watchers
    watch(tasks, () => {
      console.log('Task list updated:', tasks.value);
    });

    return {
      tasks,
      newTaskTitle,
      newTaskPriority,
      sortedTasks,
      pendingTasks,
      addTask,
      toggleTask,
      deleteTask,
    };
  },
});
</script>

<style scoped>
/* Basic Styling */
/* General Styling */
.todo-app {
  font-family: 'Roboto', sans-serif;
  background-color: #f4f7fb;
  color: #333;
  max-width: 600px;
  margin: 40px auto;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease-in-out;
}

.todo-app h1 {
  text-align: center;
  color: #4a90e2;
  font-size: 2.5rem;
  margin-bottom: 20px;
}

.new-task {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
  border-bottom: 2px solid #e0e0e0;
  padding-bottom: 20px;
}

.new-task input,
.new-task select,
.new-task button {
  padding: 12px;
  border-radius: 8px;
  font-size: 1rem;
  border: 1px solid #ddd;
  box-sizing: border-box; /* Ensure padding and border are included in total width/height */
}

.new-task input,
.new-task select {
  width: 100%; /* Ensure both input and select take full width */
}

.new-task button {
  background-color: #4a90e2;
  color: #fff;
  cursor: pointer;
  border: none;
  transition: background-color 0.3s ease;
}

.new-task button:hover {
  background-color: #357ab7;
}

/* Task List Styling */
.task-list {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px;
  margin-bottom: 10px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  background-color: #fff;
  transition: all 0.3s ease;
}

li:hover {
  transform: scale(1.02);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}

li.completed span {
  text-decoration: line-through;
  color: #9e9e9e;
}

li.low {
  background-color: #e0f7fa;
}

li.medium {
  background-color: #fff3e0;
}

li.high {
  background-color: #ffebee;
}

/* Task Text Styling */
span {
  flex: 1;
  font-size: 1.1rem;
  cursor: pointer;
  transition: color 0.3s ease;
}

span:hover {
  color: #4a90e2;
}

/* Task Button Styling */
button {
  padding: 6px 12px;
  border: none;
  background-color: #f44336;
  color: #fff;
  font-size: 0.9rem;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #d32f2f;
}

/* Transition Effects */
.task-enter-active,
.task-leave-active {
  transition: all 0.5s ease-in-out;
}

.task-enter-from,
.task-leave-to {
  opacity: 0;
  transform: translateY(20px);
}

.task-enter-to,
.task-leave-from {
  opacity: 1;
  transform: translateY(0);
}

/* Pending Tasks Count */
p {
  text-align: center;
  font-size: 1.2rem;
  color: #333;
  margin-top: 20px;
  font-weight: bold;
}

</style>
