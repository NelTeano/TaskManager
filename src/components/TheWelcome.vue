<template>
  <div id="app" class="container">
    <h1>Task Manager</h1>
    <div class="form">
      <form @submit.prevent="addTask">
        <input v-model="newTaskTitle" placeholder="New Task Title" required />
        <input v-model="newTaskStatus" placeholder="New Task Status" required />
        <button type="submit">Add Task</button>
      </form>
    </div>
    <div>
      <h2>Tasks List</h2>
      <table>
        <thead>
          <tr>
            <th>Title</th>
            <th>Status</th>
            <th>Update/Delete</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="task in tasks" :key="task.id">
            <td>{{ task.title }}</td>
            <td>{{ task.status }}</td>
            <td>
              <button @click="completeTask(task.id)">Complete Task</button>
              <button @click="deleteTask(task.id)">Delete Task</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const tasks = ref([]);
const newTaskTitle = ref('');
const newTaskStatus = ref('');



const fetchTasks = async () => {
  try {
    console.log('Fetching tasks...');
    const response = await axios.get('http://localhost:8000/api/');
    console.log('Response:', response.data);
    tasks.value = response.data;
  } catch (error) {
    console.error('Error fetching tasks:', error);
  }
};

// Add a new task
const addTask = async () => {
  try {
    const newTask = {
      title: newTaskTitle.value,
      status: newTaskStatus.value
    };
    console.log('Adding task:', newTask);
    await axios.post('http://localhost:8000/api/task', newTask);
    newTaskTitle.value = '';
    newTaskStatus.value = '';
    await fetchTasks(); // Refresh the task list
  } catch (error) {
    console.error('Error adding task:', error);
  }
};

// Complete a task
const completeTask = async (taskId) => {
  try {
    console.log(`Completing task with ID: ${taskId}`);
    await axios.patch(`http://localhost:8000/api/task/${taskId}/status`, { status: 'completed' });
    await fetchTasks(); // Refresh the task list
  } catch (error) {
    console.error('Error completing task:', error);
  }
};

// Delete a task
const deleteTask = async (taskId) => {
  try {
    console.log(`Deleting task with ID: ${taskId}`);
    await axios.delete(`http://localhost:8000/api/task/${taskId}`);
    await fetchTasks(); // Refresh the task list
  } catch (error) {
    console.error('Error deleting task:', error);
  }
};

// Fetch tasks when the component is mounted
onMounted(fetchTasks);
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

button {
  margin-left: 10px;
}

.container{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center
}

.form form{
  display: inline-block;
}

.container div{
  width: 400px;
}
</style>
