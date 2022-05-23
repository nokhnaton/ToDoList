<script setup>
import axios from "axios";
//import router from "./router.js";
import { useRouter } from "vue-router";
const router = useRouter();

const logout = async () => {
  axios.get("/api/logout").then((resopnse) => {
    if (resopnse.status === 200) {
      console.log("logout");
      router.push({ path: "/", force: true });
      console.log("logout2");
    }
  });
};
</script>

<template>
  <main>
    <div :class="$style.container">
      <header :class="$style.header">
        <router-link to="/">Home</router-link>
        |
        <router-link to="/city/Tokyo">Tokyo</router-link>
        |
        <router-link to="/axios">Axios</router-link>
        |
        <router-link to="/countries">Countries</router-link>
        |
        <router-link to="/login">Login</router-link>
        |
        <router-link to="/signup">Signup</router-link>
        |
        <a href="/" @click="logout">Logout</a>
      </header>

      <router-view />
    </div>
  </main>
</template>

<style module>
.container {
  max-width: fit-content;
  margin: auto;
  text-align: center;
}
.header {
  display: flex;
  justify-content: center;
}
</style>
