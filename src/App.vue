<template>
  <div id="app">
    <SingleRepo repoName="Repository Name" />
  </div>
</template>

<script>
import SingleRepo from "./components/SingleRepo.vue";

export default {
  name: "App",
  components: {
    SingleRepo
  }
};
</script>

<style lang="scss">
#app {
  margin: 40px 0;
}
</style>
