<template>
  <div id="app">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-lg-10">
          <SingleRepo 
            v-for="repo in repos"
            :key="repo.id"
            :repoTitle="repo.name"
            :repoUserAvatar="repo.owner.avatar_url"
            :repoURL="repo.html_url"
            :repoDesc="repo.description"
            :repoNumStars="repo.stargazers_count"
            :repoNumIssues="repo.open_issues_count"
            :repoUserURL="repo.owner.html_url"
            :repoUsername="repo.owner.login"
            :repoDaysAgo="daysAgo(repo.created_at)"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import SingleRepo from "./components/SingleRepo.vue";
import axios from "axios";
import moment from "moment";

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
    },
    daysAgo(date) {
      return moment().diff(moment(date), "days");
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
