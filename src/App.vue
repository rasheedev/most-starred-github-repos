<template>
  <div id="app">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-lg-10">
          <!-- Displaying Repositories -->
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
          <!-- Spinner before each request to get repos -->
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
    // Method to get the most starred Github repos that were created in the last 30 days
    getRepos(pageNb, dateFrom, reposPerPage = 30) {
      const baseURL = `https://api.github.com/search/repositories?q=created:>${dateFrom}&sort=stars&order=desc&page=${pageNb}&per_page=${reposPerPage}`;
      return axios
        .get(baseURL)
        .then(result => {
          this.repos = this.repos.concat(result.data.items);
          this.spinner = false;
        })
        .catch(error => {
          this.spinner = false;
          console.log(error.response);
        });
    },
    // Method to get new repositories when the scroll reachs the bottom of the page
    scroll() {
      window.onscroll = () => {
        let winHeight = document.documentElement.scrollTop + window.innerHeight;
        let offHeight = document.documentElement.offsetHeight;
        if (winHeight === offHeight) {
          if (this.spinner === false) {
            this.spinner = true;
            this.pageNumber++;
            this.getRepos(this.pageNumber, this.startDate(30));
          }
        }
      };
    },
    // Method to calculate the number of days since the repo creation date using moment.js
    daysAgo(date) {
      return moment().diff(moment(date), "days");
    },
    // Method to calculate the date from which we're going to start getting repos using moment.js
    startDate(days) {
      return moment()
        .subtract(days, "days")
        .format("YYYY-MM-DD");
    },
    // Method to shorten long repo name
    setRepoName(name) {
      if (name && name.length > 40) {
        return name.substring(0, 40) + "...";
      } else if (name && name.length <= 40) {
        return name;
      }
    },
    // Method to shorten long repo descroptions
    setRepoDesc(desc) {
      if (desc && desc.length > 250) {
        return desc.substring(0, 250) + "...";
      } else if (desc && desc.length <= 250) {
        return desc;
      }
    }
  },
  // displaying repositories after the vue instance is created
  created() {
    this.getRepos(this.pageNumber, this.startDate(30));
    this.scroll();
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
