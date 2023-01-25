<template>
  <div class="container">
    <HeaderApp @toggle-add-task="toggleAddTask" title="Task Tracker" :showAddTask="showAddTask" />
    <div v-show="showAddTask">
      <AddTask @add-task="addTask" />
    </div>
    <TasksApp @toggle-reminder="toggleReminder" @delete-task="deleteTask" :tasks="tasks" />
    <FooterApp />
  </div>
</template>

<script>
import HeaderApp from './components/HeaderApp'
import FooterApp from './components/FooterApp.vue'
import TasksApp from './components/TasksApp.vue'
import AddTask from './components/AddTask.vue'

export default {
  name: 'App',
  components: {
    HeaderApp,
    FooterApp,
    TasksApp,
    AddTask
  },
  data () {
    return {
      tasks: [],
      showAddTask: false
    }
  },
  methods: {
    // set the property (showAddTask) to whatever it's not
    toggleAddTask () {
      this.showAddTask = !this.showAddTask
    },
    async addTask (task) {
      const res = await fetch('api/tasks', {
        method: 'POST',
        headers: {
          'Content-type': 'application/json'
        },
        body: JSON.stringify(task)
      })
      const data = await res.json()

      this.tasks = [...this.tasks, data]
    },
    // to delete it on click
    async deleteTask (id) {
      if (confirm('Are you sure?')) {
        const res = await fetch(`api/tasks/${id}`, {
          method: 'DELETE'
        })

        res.status === 200 ? (this.tasks = this.tasks.filter((task) => task.id !== id)) : alert('Error deleting task')
      }
    },
    // да се смени reminder-от од true во false, т.е. од зелено да е (маркирано) во тоа да не е
    async toggleReminder (id) {
      const taskToToggle = await this.fetchTask(id)
      const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder }

      const res = await fetch(`api/tasks/${id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(updTask)
      })
      const data = await res.json()
      this.tasks = this.tasks.map((task) => task.id === id ? { ...task, reminder: data.reminder } : task)
    },
    async fetchTasks () {
      const res = await fetch('api/tasks')
      const data = await res.json()

      return data
    },
    // a single task
    async fetchTask (id) {
      const res = await fetch(`api/tasks/${id}`)
      const data = await res.json()

      return data
    },
    async created () {
      this.tasks = await this.fetchTasks()
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: 'Poppins', sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
</style>
