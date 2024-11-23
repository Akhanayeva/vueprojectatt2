<template>
  <div>
    <h1>To-Do List</h1>
    <div>
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
    <transition-group name="list" tag="ul">
      <li
        v-for="task in sortedTasks"
        :key="task.id"
        :class="[{ completed: task.completed }, task.priority]"
      >
        <span @click="toggleTask(task.id)">{{ task.title }}</span>
        <button @click="deleteTask(task.id)">Delete</button>
      </li>
    </transition-group>
  </div>
</template>

<script lang="ts">
import { ref, computed, watch, nextTick } from 'vue';
import { Task } from '../types/Task';

export default {
  setup() {
    const tasks = ref<Task[]>([]);
    const newTaskTitle = ref('');
    const newTaskPriority = ref<Task['priority']>('low');

    const sortedTasks = computed(() => {
      return [...tasks.value].sort((a, b) =>
        a.priority.localeCompare(b.priority)
      );
    });

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

        await nextTick();
        const taskList = document.querySelector('ul');
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

    watch(tasks, () => {
      console.log('Task list updated');
    });

    return {
      tasks,
