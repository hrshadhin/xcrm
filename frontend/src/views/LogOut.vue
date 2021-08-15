<template></template>

<script>
import axios from "axios";

export default {
  name: "LogIn",
  mounted() {
    this.logOut();
  },
  methods: {
    async logOut() {
      await axios
        .post("/api/v1/token/logout")
        .then((response) => {
          console.log("Logged out");
        })
        .catch((error) => {
          console.log(JSON.stringify(error));
        });

      axios.defaults.headers.common["Authorization"] = "";
      localStorage.removeItem("token");
      this.$store.commit("removeToken");
      localStorage.removeItem("username");
      localStorage.removeItem("userid");
      localStorage.removeItem("team_name");
      localStorage.removeItem("team_id");
      localStorage.removeItem("team_plan");
      localStorage.removeItem("team_max_leads");
      localStorage.removeItem("team_max_clients");
      this.$router.push("/");
    },
  },
};
</script>
