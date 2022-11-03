<template>
  <div class="home">
    <!-- <header class="app-header"></header> -->
    <div v-if="user">
    <Memo user="user" />
    </div>
  </div>
</template>
<script>
// @ is an alias to /src
import Memo from "../views/Memo.vue";
import { Auth } from "@aws-amplify/auth";
import { message } from "ant-design-vue";

export default {
  name: "Home",
  data() {
    return { user: null };
  },
  components: {
    Memo,
  },
  beforeMount: function() {
    Auth.currentAuthenticatedUser()
      .then((userData) => {
        console.log("home userdata:", userData);
        this.user = userData;
      })
      .catch(() => {
        message
          .warning("User not signed In")
          .then(() => this.$router.push("/login/"));
      });
  },
};
</script>
