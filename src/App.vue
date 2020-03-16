<template>
  <div id="app">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-lg-10">
          <SingleRepo 
            v-for="repo in repos"
            :key="repo.id"
            :repoTitle="setRepoName(repo.name)"
            :repoFullTitle="repo.name"
            :repoUserAvatar="repo.owner.avatar_url"
            :repoURL="repo.html_url"
            :repoDesc="setRepoDesc(repo.description)"
            :repoNumStars="repo.stargazers_count"
            :repoNumIssues="repo.open_issues_count"
            :repoUserURL="repo.owner.html_url"
            :repoUsername="repo.owner.login"
            :repoDaysAgo="daysAgo(repo.created_at)"
          />
          <grid-loader class="loader" v-show="spinner"></grid-loader>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import SingleRepo from "./components/SingleRepo.vue";
import axios from "axios";
import moment from "moment";
import GridLoader from "vue-spinner/src/GridLoader.vue";

export default {
  name: "App",
  components: {
    SingleRepo,
    GridLoader
  },
  data() {
    return {
      repos: [],
      pageNumber: 1,
      spinner: true
    };
  },
  methods: {
    getRepos(pageNb, dateFrom) {
      const baseURL = `https://api.github.com/search/repositories?q=created:>${dateFrom}&sort=stars&order=desc&page=${pageNb}`;
      return axios
        .get(baseURL)
        .then(result => {
          this.repos = this.repos.concat(result.data.items);
          this.spinner = false;
          console.log(this.repos);
        })
        .catch(error => {
          this.spinner = false;
          console.log(error.response);
        });
    },
    daysAgo(date) {
      return moment().diff(moment(date), "days");
    },
    startDate(days) {
      return moment()
        .subtract(days, "days")
        .format("YYYY-MM-DD");
    },
    setRepoName(name) {
      if (name && name.length > 40)
        return name.substring(0, 40) + "...";
      else if(name && name.length <= 40)
        return name;
    },
    setRepoDesc(desc) {
      if (desc && desc.length > 250)
        return desc.substring(0, 250) + "..." 
      else if(desc && desc.length <= 250)
        return desc;
    }
  },
  created() {
    this.getRepos(this.pageNumber, this.startDate(30));
  }
};
</script>

<style lang="scss">
#app {
  margin: 40px 0;
  .loader {
    text-align: center;
    margin: 0 auto;
  }
}
</style>
