<template>
  <div>
    <div class="task-form">
      <button
        v-if="isNewTask"
        type="button"
        class="btn btn-primary add-btn"
        data-bs-toggle="modal"
        :data-bs-target="'#' + bsTarget"
      >
        +
      </button>

      <div
        class="modal fade"
        :id="bsTarget"
        data-bs-backdrop="static"
        data-bs-keyboard="false"
        tabindex="0"
        aria-labelledby="staticBackdropLabel"
        aria-hidden="true"
      >
        <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="staticBackdropLabel">
                {{
                  isNewTask
                    ? "Créer une nouvelle tache"
                    : "Modifier les informations sur une tache"
                }}
              </h5>
              <button
                type="button"
                class="btn-close"
                data-bs-dismiss="modal"
                aria-label="Close"
              ></button>
            </div>
            <div class="modal-body">
              <form>
                <div class="mb-3">
                  <label for="recipient-name" class="col-form-label"
                    >Titre de la tache</label
                  >
                  <input
                    type="text"
                    class="form-control"
                    v-model="task.title"
                  />
                </div>

                <div class="mb-3">
                  <label for="recipient-name" class="col-form-label"
                    >Description de la tache</label
                  >
                  <textarea
                    class="form-control"
                    v-model="task.description"
                  ></textarea>
                </div>

                <div class="mb-3">
                  <label for="recipient-name" class="col-form-label"
                    >Date limite</label
                  >
                  <input type="date" class="form-control" v-model="task.date" />
                </div>
              </form>
            </div>
            <div class="modal-footer">
              <button
                type="button"
                class="btn btn-outline-success"
                data-bs-dismiss="modal"
                @click="isNewTask == true ? addTask() : updateTask()"
              >
                {{
                  isNewTask
                    ? "Ajouter la tache"
                    : "Modifier les informations de la tache"
                }}
              </button>
              <button
                type="button"
                class="btn btn-outline-danger"
                data-bs-dismiss="modal"
              >
                Annuler
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { defineComponent } from "vue";
import { v4 as uuidv4 } from "uuid";
import axios from "axios";
const baseUrl = "http://localhost:3004";
export default defineComponent({
  name: "TaskForm",
  props: ["isNewTask", "_task", "currentUser", "bsTarget"],
  data() {
    return {
      errors: [],
      task: this._task,
    };
  },
  created() {
    console.log("the Task", this.isNewTask);
    if (this.isNewTask || this._task === undefined || this._task == null)
      this.task = {};
  },
  methods: {
    async updateTask() {
      const confirmation = confirm(
        "Voulez vous vraiment effectuer ces modifications ? elles sont irreversibles"
      );
      if (!confirmation) return alert("la tache ne sera pas modifiée");
      const res = await axios
        .put(`${baseUrl}/tasks/${this.task.id}`, this.task)
      if (res.status == 200)
        return alert("modifications effectuées avec succes");
    },
    addTask() {
      // TODO: validate input data
      this.errors = [];
      if (this.task.title.trim().length < 3)
        this.errors.push("Nom de tache trop court");

      if (this.errors.length != 0) return console.log(this.errors);

      let task = {
        id: uuidv4(),
        title: this.task.title,
        description: this.task.description,
        date: this.date,
        user: this.currentUser.email,
        isDone: false,
        tag: this.date == null ? 6 : 1,
      };
      return this.$emit("saveTask", task);
    },
  },
});
</script>

<style>
.task-form {
  position: absolute;
  right: 10px;
  bottom: 10px;
}

.add-btn {
  border-radius: 100px;
  width: 50px;
  height: 50px;
  opacity: 0.8;
}
</style>
