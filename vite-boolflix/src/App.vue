<script>
import axios from "axios";
import AppHeader from "./components/AppHeader.vue";
import AppMain from "./components/AppMain.vue";
import { store } from "./store.js";
import "@fortawesome/fontawesome-free/css/all.css";
export default {
  data() {
    return {};
  },
  components: {
    AppMain,
  },
};
</script>

<template>
  <AppMain />
</template>

<style lang="scss" scoped>
@use "bootstrap/scss/bootstrap.scss" as *;
</style>
