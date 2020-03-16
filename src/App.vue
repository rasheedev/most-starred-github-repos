<template>
  <div id="app">
    <SingleRepo 
      v-for="repo in repos"
      :key="repo.id"
      :repoName="repo.name"
    />
  </div>
</template>

<script>
import SingleRepo from "./components/SingleRepo.vue";
import axios from "axios";

export default {
  name: "App",
  components: {
    SingleRepo
  },
  data() {
    return {
      repos: [],
      pageNumber: 1
    };
  },
  methods: {
    getRepos(pageNb) {
      return axios
        .get(`https://api.github.com/search/repositories?q=created:>2017-10-22&sort=stars&order=desc&page=${pageNb}`)
        .then(result => {
          this.repos = this.repos.concat(result.data.items);
          console.log(this.repos);
        })
        .catch(error => {
          console.log(error.response);
        });
    }
  },
  created(){
    this.getRepos(this.pageNumber);
  }
};
</script>

<style lang="scss">
#app {
  margin: 40px 0;
}
</style>
