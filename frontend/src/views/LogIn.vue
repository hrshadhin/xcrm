<template>
  <div class="container">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h1 class="title">Login</h1>
        <form @submit.prevent="submitForm">
          <div class="field">
            <label>Email</label>
            <div class="control">
              <input
                type="email"
                name="email"
                class="input"
                v-model="username"
              />
            </div>
          </div>

          <div class="field">
            <label>Password</label>
            <div class="control">
              <input
                type="password"
                name="password"
                class="input"
                v-model="password"
              />
            </div>
          </div>
          <div class="notification is-danger" v-if="errors.length">
            <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
          </div>
          <div class="field">
            <div class="control">
              <button class="button is-success">Login</button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "LogIn",
  data() {
    return {
      username: "",
      password: "",
      errors: [],
    };
  },
  methods: {
    async submitForm() {
      this.$store.commit("setIsLoading", true);

      axios.defaults.headers.common["Authorization"] = "";
      localStorage.removeItem("token");

      this.errors = [];
      if (this.username == "") {
        this.errors.push("The username is missing");
      }

      if (this.password == "" || this.password.length < 8) {
        this.errors.push("The password is too short");
      }

      if (!this.errors.length) {
        const formData = {
          username: this.username,
          password: this.password,
        };

        await axios
          .post("/api/v1/token/login/", formData)
          .then((response) => {
            const token = response.data.auth_token;

            this.$store.commit("setToken", token);

            axios.defaults.headers.common["Authorization"] = "Token " + token;

            localStorage.setItem("token", token);
          })
          .catch((error) => {
            this.showErrors(error);
          });

        await axios
          .get("/api/v1/users/me")
          .then((response) => {
            this.$store.commit("setUser", {
              id: response.data.id,
              username: response.data.username,
            });
            localStorage.setItem("userid", response.data.id);
            localStorage.setItem("username", response.data.username);

            this.$router.push("/dashboard/my-account");
          })
          .catch((error) => {
            this.showErrors(error, true);
          });

        await axios
          .get("/api/v1/teams/get_my_team/")
          .then((response) => {
            this.$store.commit("setTeam", {
              id: response.data.id,
              name: response.data.name,
              plan: response.data.plan.name,
              max_leads: response.data.plan.max_leads,
              max_clients: response.data.plan.max_clients,
            });

            this.$router.push("/dashboard/my-account");
          })
          .catch((error) => {
            this.showErrors(error, true);
          });

        this.$store.commit("setIsLoading", false);
      }
    },
    showErrors(error, logInConsole = false) {
      if (logInConsole) {
        console.log(error);
        this.errors.push("Something went wrong. Check log for details.");
        return true;
      }

      if (error.response) {
        for (const property in error.response.data) {
          this.errors.push(`${error.response.data[property]}`);
        }
      } else if (error.message) {
        this.errors.push("Something went wrong. Please try again");
      }
    },
  },
};
</script>
