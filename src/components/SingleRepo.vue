<template>
  <div class="repo">
    <h1> {{ repoName }} </h1>
  </div>
</template>

<script>
export default {
  name: "SingleRepo",
  props: {
    repoName: String
  }
};
</script>

<style scoped lang="scss">

</style>
