<template>
  <div class="container">
    <h1 class="text-center mt-5">TodoList VueJs</h1>
    <div class="d-flex">
      <input type="text" class="form-control" v-model="task" />
      <button class="btn btn-warning" @click="SaveTask()">Submit</button>
    </div>

    <table class="table table-bordered">
      <thead>
        <tr>
          <th scope="col">Task</th>
          <th scope="col">Status</th>
          <th scope="col">Edit</th>
          <th scope="col">Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td scope="row">{{ task.name }}</td>
          <td @click="ToggleStatus(index)">{{ task.status }}</td>
          <td class="text-center" @click="EditTask(index)">
            <i class="fa-solid fa-pen-to-square"></i>
          </td>
          <td class="text-center" @click="DeleteTask(index)">
            <i class="fa-solid fa-trash"></i>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "TodoList",
  data() {
    return {
      task: "",
      status: ["to-do", "in-progress", "done"],
      newIndex: null,
      tasks: [],
    };
  },
  created() {
    axios.get("http://localhost:3005/tasks").then((response) => {
      this.tasks = response.data;
    });
  },
  methods: {
    DeleteTask(index) {
      axios.delete(`http://localhost:3005/tasks/${index}`).then(() =>
        axios.get("http://localhost:3005/tasks").then((response) => {
          this.tasks = response.data;
        })
      );
    },
    SaveTask() {
      if (this.task.length === 0) return;
      if (this.newIndex === null) {
        axios
          .post("http://localhost:3005/tasks", {
            name: this.task,
            status: "to-do",
          })
          .then(() =>
            axios.get("http://localhost:3005/tasks").then((response) => {
              this.tasks = response.data;
            })
          );

        /*
        this.tasks = [
          {
            name: this.task,
            status: "to-do",
          },
          ...this.tasks,
        ]; */
      } else {
        this.tasks[this.newIndex].name = this.task;
        axios
          .put(`http://localhost:3005/tasks/${this.newIndex}`, this.tasks[this.newIndex])
          .then(() =>
            axios.get("http://localhost:3005/tasks").then((response) => {
              this.tasks = response.data;
            })
          );
      }
      this.task = "";
      this.newIndex = null;
    },
    EditTask(index) {
      this.task = this.tasks[index].name;
      this.newIndex = index;
    },
    ToggleStatus(index) {
      let statusIndex = this.status.indexOf(this.tasks[index].status);
      if (statusIndex == this.status.length - 1) statusIndex = -1;
      this.tasks[index].status = this.status[statusIndex + 1];

      axios
        .put(`http://localhost:3005/tasks/${index}`, this.tasks[index])
        .then(() =>
          axios.get("http://localhost:3005/tasks").then((response) => {
            this.tasks = response.data;
          })
        );
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
