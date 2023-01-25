<template>
    <div :key="task.id" v-for="task in tasks">
      <TaskApp @toggle-reminder="$emit('toggle-reminder', task.id)" @delete-task="$emit('delete-task', task.id)" :task='task' />
    </div>
</template>

<script>
import TaskApp from './TaskApp.vue'

export default {
  name: 'TasksApp',
  props: {
    tasks: Array
  },
  components: {
    TaskApp
  },
  emits: ['delete-task', 'toggle-reminder']
}
</script>
