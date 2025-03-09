<template>
  <div>
    <AddTaskModal
      :modalAddActive="modalAddActive"
      @close="modalAddActive = false"
      @task-added="updateTaskInList"
    />
    <EditTaskModal
      :modalEditActive="modalEditActive"
      :id="selectedId"
      @close="modalEditActive = false"
      @task-updated="updateTaskInList"
    />
    <h1>To Do List</h1>
    <br />
    <button type="primary" class="button" @click="modalAddActive = true">
      <font-awesome-icon :icon="['fas', 'plus']" class="fa-2x" /> Add Task
    </button>
    <div class="table-wrapper mt-3">
      <table id="tableComponent" class="table table-borderless table-striped">
        <thead>
          <tr>
            <th class="align-middle text-center">ID</th>
            <th class="align-middle text-left">Name</th>
            <th class="align-middle text-left">Priority</th>
            <th class="align-middle text-left">Deadline</th>
            <th class="align-middle text-center">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in tasks.data" :key="item.Id">
            <td class="align-middle text-center">{{ item.Id }}</td>
            <td class="align-middle text-left">{{ item.Name }}</td>
            <td class="align-middle text-left">
              <button
                v-if="item.Priority === 'High'"
                class="btn-high"
                style="cursor: default"
              >
                High
              </button>
              <button
                v-else-if="item.Priority === 'Medium'"
                class="btn-medium"
                style="cursor: default"
              >
                Medium
              </button>
              <button
                v-else-if="item.Priority === 'Low'"
                class="btn-low"
                style="cursor: default"
              >
                Low
              </button>
            </td>
            <td class="align-middle text-left">{{ item.Deadline }}</td>
            <td class="align-middle text-center">
              <font-awesome-icon
                :icon="['fas', 'edit']"
                class="fa-2x mx-2"
                style="color: #624de3"
                @click="openEditModal(item.Id)"
              />
              <font-awesome-icon
                :icon="['fas', 'trash-alt']"
                class="fa-2x mx-2"
                style="color: #a30d11"
                @click="deleteTask(item.Id)"
              />
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import AddTaskModal from "../AddTaskModal.vue";
import EditTaskModal from "../EditTaskModal.vue";

export default {
  components: {
    AddTaskModal,
    EditTaskModal,
  },
  methods: {
    formatDateToIndonesian(dateString) {
      if (!dateString) return "";

      const date = new Date(dateString);
      const months = [
        "Januari",
        "Februari",
        "Maret",
        "April",
        "Mei",
        "Juni",
        "Juli",
        "Agustus",
        "September",
        "Oktober",
        "November",
        "Desember",
      ];

      const day = date.getDate();
      const month = months[date.getMonth()];
      const year = date.getFullYear();

      return `${day} ${month} ${year}`;
    },
    async fetchData() {
      const response = await fetch(
        "https://todolist-backend-production-7602.up.railway.app/task"
      );
      this.tasks = await response.json();
    },

    async deleteTask(id) {
      await fetch(
        `https://todolist-backend-production-7602.up.railway.app/task/${id}`,
        { method: "DELETE" }
      );
      this.tasks.data = this.tasks.data.filter((task) => task.Id !== id);
    },
    openEditModal(id) {
      this.selectedId = id;
      console.log(this.selectedId);
      this.modalEditActive = true;
    },
    updateTaskInList() {
      this.fetchData();
    },
  },
  mounted() {
    this.fetchData();
  },
  data() {
    return {
      modalAddActive: false,
      modalEditActive: false,
      tasks: [],
      selectedId: null,
    };
  },
};
</script>

<style scoped>
.table-wrapper {
  max-width: 1300px;
  border: 2px solid rgba(0, 0, 0, 0.073);
  border-radius: 15px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.073);
}

.table-borderless {
  font-size: 20px;
  font-family: Sofia Sans;
  margin-bottom: 0px;
}

h1 {
  font-family: Sofia Sans;
  font-size: 48px;
}

.button {
  border: none;
  padding: 6px 24px;
  background: #00c632;
  height: 41px;
  width: 150px;
  border-radius: 15px;
  cursor: pointer;
  color: white;
  font-size: 20px;
  font-family: Sofia Sans;
}

.btn-high {
  border: none;
  border-radius: 22px;
  width: 120px;
  height: 40px;
  background: #fbe7e8;
  color: #a30d11;
}

.btn-medium {
  border: none;
  border-radius: 22px;
  width: 120px;
  height: 40px;
  background: #fef2e5;
  color: #cd6200;
}

.btn-low {
  border: none;
  border-radius: 22px;
  width: 120px;
  height: 40px;
  background: #daffea;
  color: #1f9254;
}

.fa-2x {
  font-size: 16px;
  cursor: pointer;
}
</style>
