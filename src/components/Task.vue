<template>
  <div class="card task-card">
    <TaskForm
      @saveTask="saveTask"
      :bs-target="'updateTaskModal' + task.id"
      :currentUser="currentUser"
      :isNewTask="false"
      :_task="task"
    />

    <div class="card-header">
      <ul class="nav nav-tabs card-header-tabs">
        <li class="nav-item">
          <span class="nav-link active">Tâche</span>
        </li>
        <li class="nav-item">
          <span class="nav-link">Assignations</span>
        </li>
        <li class="nav-item">
          <span class="nav-link">Comments</span>
        </li>
      </ul>
    </div>
    <div class="card-body">
      <h5 class="card-title">{{ task.title }}</h5>
      <p class="card-text">{{ task.description }}</p>
    </div>

    <div class="card-footer">
      <span
        data-placement="top"
        data-bs-toggle="modal"
        :data-bs-target="'#updateTaskModal' + task.id"
        data-toggle="tooltip"
        title="Modifier la tâche"
      >
        <i class="bi bi-pen btn btn-outline-warning"></i>
      </span>

      <span
        v-if="!task.isDone"
        data-placement="top"
        data-toggle="tooltip"
        title="Marquer la tache comme accomplie"
      >
        <i
          class="bi bi-check btn btn-outline-success"
          @click="markAsDone()"
        ></i>
      </span>

      <span
        data-placement="top"
        data-toggle="tooltip"
        title="Changer l'etiquette de la tache"
      >
        <i
          class="bi bi-person-add btn btn-outline-info"
          @click="changeTag()"
        ></i>
      </span>
      <span
        data-placement="top"
        data-toggle="tooltip"
        title="Supprimer la tâche"
      >
        <i
          class="bi bi-trash3 btn btn-outline-danger"
          @click="deleteTask()"
        ></i>
      </span>
    </div>
  </div>
</template>

<script>
import { defineComponent } from "vue";
import axios from "axios";
import TaskForm from "./TaskForm.vue";
const baseUrl = "http://localhost:3004";
export default defineComponent({
  name: "TaskComponent",
  props: ["task", "currentUser"],
  emits: ["updateVisibleTasks"],
  components: { TaskForm },

  data() {
    return {
      task: this.task,
    };
  },
  methods: {
    async markAsDone() {
      this.task.isDone = true;
      let res = await axios.put(`${baseUrl}/tasks/${this.task.id}`, this.task);
      if (res.status == 200) {
        // TODO: update task tag
      }
      this.$emit("updateVisibleTasks");
    },
    async deleteTask() {
      // TODO: check authorizations and roles before starting

      let confirmation = confirm("Do you want to delete this tag ?");
      if (!confirmation) return alert("the task will not be deleted");

      let res = await axios.delete(`${baseUrl}/tasks/${this.task.id}`);

      if (res.status != 200) alert("Error occured while deleting task");

      this.$emit("updateVisibleTasks");
    },
  },
});
</script>

<style>
.task-card {
  display: flex;
  text-align: left;
  padding: 0;
  min-width: 360px;
  max-width: 32%;
}

.card-footer {
  display: flex;
  align-items: center;

  justify-content: space-evenly;
}
</style>
