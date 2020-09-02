<template>
  <div>
    <div v-if="firstView" class="searchFont">输入用户名搜索</div>
    <div v-if="loading" class="searchFont">LOADING...</div>
    <div v-if="errorMsg" class="searchFont">{{errorMsg}}</div>
    <div class="row">
      <div class="card" v-for="(user, index) in users" :key="index">
        <a :href="user.url" target="_blank">
          <img :src="user.avatar_url" style="width: 100px" />
        </a>
        <p class="card-text">{{user.name}}</p>
      </div>
    </div>
  </div>
</template>

<script>
import PubSub from "pubsub-js";
import axios from "axios";
export default {
  data() {
    return {
      firstView: true,
      loading: false,
      errorMsg: "",
      users: null,
    };
  },
  mounted() {
    PubSub.subscribe("search", (msg, searchName) => {
      const url = `https://api.github.com/search/users?q=${searchName}`;
      this.firstView = false;
      this.loading = true;
      this.users = null;
      this.errorMsg = '';
      axios
        .get(url)
        .then((response) => {
          const rezult = response.data;
          const users = rezult.items.map((item) => ({
            url: item.html_url,
            avatar_url: item.avatar_url,
            name: item.login,
          }));
          this.loading = false;
          this.users = users;
        })
        .catch((error) => {
          this.loading = false;
          this.errorMsg = error;
        });
    });
  },
};
</script>

<style>
.card {
  float: left;
  width: 33.333%;
  padding: 0.75rem;
  margin-bottom: 2rem;
  border: 1px solid #efefef;
  text-align: center;
}

.card > img {
  margin-bottom: 0.75rem;
  border-radius: 100px;
}

.card-text {
  font-size: 85%;
}

.searchFont {
  font-size: 50px;
}
</style>