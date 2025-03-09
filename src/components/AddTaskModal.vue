<template>
  <transition name="modal-animation">
    <div v-show="modalAddActive" class="modal">
      <transition name="modal-animation-inner">
        <div v-show="modalAddActive" class="modal-inner" ref="modal">
          <div class="modal-head">
            <h2 class="modal-title">Add New Task</h2>
            <font-awesome-icon
              @click="$emit('close')"
              :icon="['fas', 'x']"
              class="fa-2x close-icon"
              style="color: #cacaca; cursor: pointer"
            />
          </div>

          <form @submit.prevent="submitTask">
            <div class="form-container">
              <div class="form-group">
                <label for="taskName">Task Name</label>
                <input
                  required
                  type="text"
                  id="taskName"
                  v-model="taskName"
                  placeholder="Enter task name"
                  class="form-input"
                />
              </div>

              <div class="form-group">
                <label for="priority">Priority</label>
                <select id="priority" v-model="priority" class="form-select">
                  <option value="Low">Low</option>
                  <option value="Medium">Medium</option>
                  <option value="High">High</option>
                </select>
              </div>

              <div class="form-group">
                <label for="deadline">Deadline</label>
                <input
                  required
                  type="date"
                  id="deadline"
                  v-model="deadline"
                  class="form-input"
                />
              </div>
            </div>

            <!-- Footer with buttons -->
            <div class="modal-foot">
              <button @click="$emit('close')" class="btn-cancel">Cancel</button>

              <button class="btn-submit" type="submit">Submit</button>
            </div>
          </form>
        </div>
      </transition>
    </div>
  </transition>
</template>

<script>
export default {
  props: ["modalAddActive"],
  emits: ["close", "task-added"],
  data() {
    return {
      taskName: "",
      priority: "",
      deadline: "",
    };
  },
  watch: {
    modalAddActive(newVal, oldVal) {
      if (newVal === true && oldVal === false) {
        this.resetForm();
      }
    },
  },
  methods: {
    async submitTask() {
      console.log(this.deadline);
      const newTask = {
        Name: this.taskName,
        Priority: this.priority,
        Deadline: this.deadline,
      };

      try {
        const response = await fetch(
          "http://todolist-backend-production-7602.up.railway.app/task",
          {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(newTask),
          }
        );

        if (response.ok) {
          this.$emit("task-added");
          this.$emit("close");
        } else {
          console.error("Failed to add task");
        }
      } catch (error) {
        console.error("Error:", error);
      }
    },
    resetForm() {
      this.taskName = "";
      this.priority = "Low";
      this.deadline = "";
    },
    closeOnEscape(event) {
      if (event.key === "Escape") {
        this.$emit("close");
      }
    },
    handleClickOutside(event) {
      if (this.$refs.modal && !this.$refs.modal.contains(event.target)) {
        this.$emit("close");
      }
    },
  },
  mounted() {
    document.addEventListener("keydown", this.closeOnEscape);
    document.addEventListener("click", this.handleClickOutside, true);
  },
  unmounted() {
    document.removeEventListener("keydown", this.closeOnEscape);
    document.removeEventListener("click", this.handleClickOutside, true);
  },
};
</script>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal-inner {
  background-color: white;
  padding: 24px;
  border-radius: 12px;
  width: 90%;
  max-width: 450px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.modal-head {
  padding: 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.modal-title {
  font-family: "Sofia Sans", sans-serif;
  font-size: 20px;
  margin: 0;
}

.close-icon {
  font-size: 16px;
  color: #cacaca;
}

.form-container {
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 16px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-family: "Sofia Sans", sans-serif;
  font-weight: 500;
}

.form-input,
.form-select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-family: "Sofia Sans", sans-serif;
  font-size: 16px;
}

.modal-foot {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 15px;
  padding: 0;
}

.btn-cancel,
.btn-submit {
  flex: 1;
  padding: 10px 16px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-family: "Sofia Sans", sans-serif;
  font-size: 18px;
  max-width: calc(50% - 6px);
}

.btn-cancel {
  background-color: white;
  border: 1px solid black;
}

.btn-submit {
  border: none;
  background-color: #a020f0;
  color: white;
}

.modal-animation-enter-active,
.modal-animation-leave-active {
  transition: opacity 0.3s ease;
}

.modal-animation-enter-from,
.modal-animation-leave-to {
  opacity: 0;
}

.modal-animation-inner-enter-active,
.modal-animation-inner-leave-active {
  transition: all 0.3s ease;
}

.modal-animation-inner-enter-from,
.modal-animation-inner-leave-to {
  opacity: 0;
  transform: scale(0.8);
}
</style>
