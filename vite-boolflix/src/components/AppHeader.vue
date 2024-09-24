<script>
import AppSearch from "./AppSearch.vue";
import { store } from "../store.js";
export default {
  data() {
    return {
      store,
    };
  },
  components: {
    AppSearch,
  },
};
</script>

<template>
  <AppSearch />
</template>

<style lang="scss" scoped>
@use "bootstrap/scss/bootstrap.scss" as *;
</style>
