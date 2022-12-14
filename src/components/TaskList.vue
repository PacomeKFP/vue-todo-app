<template>
  <div class="app-container" id="tasklist">
    <h1 class="app-header">

      <span v-for="tag in taskTags" class="chip" :class="{ 'chip-active': activeTag == taskTags.indexOf(tag) }"
        @click="() => { getTaskWithTag(taskTags.indexOf(tag)) }"> {{ tag }} {{ (activeTag == taskTags.indexOf(tag)) ?
            'Tasks' : ''
        }} </span>

    </h1>
    <hr>

    <TaskForm bs-target="addTaskModal" @saveTask="saveTask" :currentUser="currentUser" :isNewTask="true" :_task="null" />

    <br><br>
    <div class="task-list">
      <TaskComponent v-for="task in visibleTask" :task="task" key="task.id"
        @updateVisibleTasks="console.log('emittion')" />
    </div>
  </div>

</template>

<script>
import { defineComponent } from 'vue';
import TaskForm from './TaskForm.vue';
import TaskComponent from './Task.vue';
import axios from 'axios';



let TaskStates = ['All', 'Pending', 'In progess', 'Done', 'Not limited in time']
// let TaskStates = ['All', 'Pending', 'In progess', 'Overdue', 'Done in time', 'Done out of time', 'Not limited in time']
let baseUrl = 'http://localhost:3004'

export default defineComponent({
  name: "TaskList",
  components: { TaskForm, TaskComponent },
  props: ['currentUser'],
  data() {
    return {
      errors: [],
      taskTags: TaskStates,
      activeTag: 0,
      tasks: [],
      visibleTask: [],
      currentUser: this.currentUser
    }
  },
  async created() {
    this.tasks = []
    this.updateTasks()
      .then(() => this.getTaskWithTag(0))
      .catch((e) => console.log(e));

  },
  methods: {
    async updateTasks() {
      let res = await axios
        .get(`${baseUrl}/tasks?user=${this.currentUser.email}`)
        .then(async (res) => await res)
      if (res.status != 200)
        return console.error(res)
      this.tasks = []
      this.tasks.push(...res.data)
    },
    getTaskWithTag(tag) {
      this.activeTag = tag
      this.visibleTask = []
      this.updateTasks()
      for (const task of this.tasks) {
        if (tag == 0) {
          this.visibleTask.push(task)
          continue
        }
        if (task.tag == tag)
          this.visibleTask.push(task)
      }

    },
    updateVisibleTasks() {
      this.updateTasks()

    },
    async saveTask(task) {
      // save the task in the data base
      let res = await axios.post(`${baseUrl}/tasks`, task).then((res) => res)
      if (res.status != 201)
        return console.error(res)

      this.tasks.push(task)

    }

  }
})

</script>
<style>
.task-list {
  display: flex;
  justify-content: space-evenly;
  flex-direction: row;
  flex-wrap: wrap;
  align-content: space-between;
}
</style>