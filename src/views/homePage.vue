<template>
  <div class="task-tracker-container">
    <h3>Task Manager</h3>
    <addTask v-if="isAuthenticated" />
    <div id="tasks" v-if="isAuthenticated">
      <h4>To be finished</h4>
      <showTask :tasks="completedTask" type="not-completed" class="task-section" />
      <h4>Finished</h4>
      <showTask :tasks="completedTask" type="completed" class="task-section" />
    </div>
    <div v-else class="welcome-message">
      <h4>Welcome to the Task Manager!</h4>
      <p>Please log in or sign up to manage your tasks.</p>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from "vuex";
import showTask from "../components/showTask.vue";
import addTask from "../components/addTask.vue";
import VueJwtDecode from "vue-jwt-decode";

export default {
  name: "TaskTracker",
  computed: {
    ...mapGetters(["allTasks"]),
    ...mapGetters("auth", ["isAuthenticated"]),
    completedTask() {
      return this.allTasks;
    },
  },
  methods: {
    ...mapActions("auth", ["login"]),
    ...mapActions(["fetchTasks"]),
  },

  mounted() {
    this.login();
    let token = localStorage.getItem("user");
    if (token != null) {
      try {
        let decoded = VueJwtDecode.decode(token);
        this.user = decoded;
        this.fetchTasks(token);
      } catch (error) {
        console.log(error, "error from decoding token");
      }
    }
  },
  components: {
    showTask,
    addTask,
  },
};
</script>

<style scoped>
.task-tracker-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h3 {
  color: #333;
}

.task-section {
  margin: 20px 0;
  width: 100%;
}

.welcome-message {
  text-align: center;
  padding: 20px;
  border: 2px dashed #ccc;
  border-radius: 8px;
  background-color: #e6f7ff;
  color: #333;
  width: 100%;
  max-width: 400px;
}

.welcome-message h4 {
  margin: 0;
  color: #007BFF;
}

.welcome-message p {
  margin-top: 10px;
  color: #555;
}
</style>
