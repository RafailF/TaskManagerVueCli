<template>
  <div>
    <h1>Task Manager</h1>
    <form @submit.prevent="addTask">
      <input type="text" v-model="newTask" placeholder="Add a new task" />
      <button type="submit">Add Task</button>
    </form>
    <ul>
      <li v-for="task in tasks" :key="task._id">
        {{ task.description }} 
        <input type="checkbox" v-model="task.completed" @change="updateTask(task)" />
        <button @click="deleteTask(task._id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [],
      newTask: ''
    };
  },
  created() {
    // Fetch tasks from backend API
    this.fetchTasks();
  },
  methods: {
    async fetchTasks() {
      try {
        const response = await fetch('http://localhost:3000/tasks');
        const tasks = await response.json();
        this.tasks = tasks;
      } catch (err) {
        console.error('Failed to fetch tasks:', err);
      }
    },
    async addTask() {
      try {
        const response = await fetch('http://localhost:3000/tasks', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({ description: this.newTask })
        });
        const task = await response.json();
        this.tasks.push(task);
        this.newTask = '';
      } catch (err) {
        console.error('Failed to add task:', err);
      }
    },
    async updateTask(task) {
      try {
        const response = await fetch(`http://localhost:3000/tasks/${task._id}`, {
          method: 'PATCH',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({ completed: task.completed })
        });
        const updatedTask = await response.json();
        this.tasks = this.tasks.map(t => (t._id === updatedTask._id ? updatedTask : t));
      } catch (err) {
        console.error('Failed to update task:', err);
      }
    },
    async deleteTask(taskId) {
      try {
        const response = await fetch(`http://localhost:3000/tasks/${taskId}`, {
          method: 'DELETE'
        });
        const deletedTask = await response.json();
        this.tasks = this.tasks.filter(t => t._id !== deletedTask._id);
      } catch (err) {
        console.error('Failed to delete task:', err);
      }
    }
  }
};
</script>
