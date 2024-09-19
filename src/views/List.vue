<template>
  <div class="container mt-2">
    <div v-for="(task, index) in tasks" :key="index">
      <b-card class="mb-2" @click="toggleCompletion(task)">
        <b-card-title>
          <span>{{ task.subject }}</span>
          <b-form-checkbox 
            v-model="task.completed" 
            class="float-right" 
            size="lg">
          </b-form-checkbox>
        </b-card-title>
        <b-card-text>
          {{ task.description }}
        </b-card-text>

        <b-button variant="outline-secondary" class="mr-2" @click.stop="edit(index)">Editar</b-button>
        <b-button variant="outline-danger" class="mr-2" @click.stop="remove(task, index)">Excluir</b-button>
      </b-card>
    </div>

    <div class="mb-3">
      <b-button variant="success" @click="addTask">
        <b-icon icon="plus" aria-hidden="true"></b-icon> Adicionar Tarefa
      </b-button>
    </div>

    <b-modal ref="modalRemove" hide-footer title="Exclusão de tarefa">
      <div class="d-block text-center">
        Deseja realmente excluir essa tarefa? {{ taskSelected.subject }}
      </div>

      <div class="mt-3 d-flex justify-content-end">
        <b-button variant="outline-secondary" class="mr-2" @click="hideModal">Cancelar</b-button>
        <b-button variant="outline-danger" class="mr-2" @click="confirmRemoveTask">Excluir</b-button>
      </div>
    </b-modal>
  </div>
</template>

<script>
import { BIcon } from 'bootstrap-vue';

export default {
  name: 'List',
  components: {
    BIcon
  },

  data() {
    return {
      tasks: [],
      taskSelected: []
    }
  },

  created() {
    this.loadTasks();
  },

  methods: {
    loadTasks() {
      this.tasks = JSON.parse(localStorage.getItem("tasks")) || [];
      this.tasks.forEach(task => {
        if (task.completed === undefined) {
          this.$set(task, 'completed', false);
        }
      });
    },
    addTask() {
      this.$router.push({ name: "form" });
    },
    edit(index) {
      this.$router.push({ name: "form", params: { index } });
    },
    remove(task, index) {
      this.taskSelected = task;
      this.taskSelected.index = index;
      this.$refs.modalRemove.show();
    },
    hideModal() {
      this.$refs.modalRemove.hide();
    },
    confirmRemoveTask() {
      this.tasks.splice(this.taskSelected.index, 1);
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
      this.hideModal();
    },
    toggleCompletion(task) {
      task.completed = !task.completed;
      this.updateTaskCompletion();
    },
    updateTaskCompletion() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    }
  }
}
</script>

<style>
.float-right {
  float: right;
}
</style>
