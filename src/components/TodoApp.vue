<template>
  <div class="container">
   
    
    <div class="d-flex justify-content-between align-items-center mt-5">
      <button class="btn btn-danger rounded-pill px-4" @click="deleteLocalStorage">Delete from Local Storage</button>
      <h2 class="text-center text-danger">Vue To-do List App</h2>
    </div>

   
    <div class="input-group mt-4">
      <input
        type="text"
        v-model="task"
        placeholder="Enter a task"
        class="form-control"
      />
      <div class="input-group-append">
        <button class="btn btn-success rounded-pill px-6 mr-2" @click="submitTask">ADD</button>
      </div>
    </div>

   
    <table class="table table-striped mt-4">
      <thead>
        <tr>
          <th scope="col">Task</th>
          <th scope="col" style="width: 100px">Status</th>
          <th scope="col" style="width: 150px">Created At</th>
          <th scope="col" class="text-center">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td>
            <span :class="{ 'line-through': task.status === 'finished' }">
              {{ task.name }}
            </span>
          </td>
          <td>
            <span
              class="badge"
              :class="{
                'badge-danger': task.status === 'to-do',
                'badge-warning': task.status === 'in-progress',
                'badge-success': task.status === 'finished',
              }"
              @click="changeStatus(index)"
            >
              {{ capitalizeFirstChar(task.status) }}
            </span>
          </td>
          <td>{{ task.created_at }}</td>
          <td class="text-center">
            <div>
              <button class="btn btn-danger btn-sm rounded-pill" @click="deleteTask(index)">
                Delete
              </button>
              <button class="btn btn-info btn-sm ml-2 rounded-pill" @click="editTask(index)">
                Edit
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "TodoApp",
  data() {
    return {
      task: "",
      editedTask: null,
      statuses: ["to-do", "in-progress", "finished"],
      tasks: [],
    };
  },
  created() {
    this.loadTasks();
  },
  methods: {
    capitalizeFirstChar(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
    changeStatus(index) {
      let newIndex = this.statuses.indexOf(this.tasks[index].status);
      newIndex = (newIndex + 1) % 3;
      this.tasks[index].status = this.statuses[newIndex];
      this.saveTasks();
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    editTask(index) {
      this.task = this.tasks[index].name;
      this.editedTask = index;
    },
    submitTask() {
      if (this.task.length === 0) return;

      if (this.editedTask !== null) {
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      } else {
        this.tasks.push({
          name: this.task,
          status: "to-do",
          created_at: new Date().toLocaleString(),
        });
      }

      this.task = "";
      this.saveTasks();
    },
    loadTasks() {
      const savedTasks = localStorage.getItem("tasks");
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }
    },
    saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    deleteLocalStorage() {
      localStorage.removeItem("tasks");
      this.tasks = []; // Clear tasks array in memory
    },
  },
};
</script>

<style>
.line-through {
  text-decoration: line-through;
}

.badge {
  cursor: pointer;
}

.input-group-append button {
  border-top-left-radius: 0;
  border-bottom-left-radius: 0;
}

input[type="text"] {
  border-radius: 0;
}

.btn-danger,
.btn-info {
  border-radius: 20px;
}

.btn-danger:hover,
.btn-info:hover {
  transform: translateY(-2px);
}

.btn-danger:focus,
.btn-info:focus {
  outline: none;
}

.btn-success {
  border-radius: 20px;
}

.btn-success:hover {
  transform: translateY(-2px);
}
</style>