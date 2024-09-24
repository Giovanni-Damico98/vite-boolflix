<script>
import AppHeader from "./AppHeader.vue";
import { store } from "../store.js";
export default {
  data() {
    return {
      AppHeader,
      store,
    };
  },
  components: {
    AppHeader,
  },
};
</script>

<template>
  <AppHeader />
</template>

<style lang="scss" scoped>
@use "bootstrap/scss/bootstrap.scss" as *;
</style>
