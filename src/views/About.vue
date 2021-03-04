<template>
  <div class="about container">
    <meta name="viewport" content="width=device-width, inital-scale=1.0" />
    <h3>Track your progress and share them!</h3>
    <div v-if="ready">
      <p>Your public profile url:</p>
      <div>
        <a id="url" :href="getUrl()"> https://prgrs.github.io{{ getUrl() }}</a>
      </div>
      <gb-button
        class="m-3"
        color="white"
        size="medium"
        left-icon="link"
        :href="getUrl()"
        >Open link</gb-button
      >
    </div>
    <gb-spinner v-else class="m-4" />
  </div>
</template>

<script>
import firebase from "firebase";

export default {
  data() {
    return {
      id: String,
      ready: false
    };
  },
  methods: {
    getUrl() {
      return "/u?id=" + this.id;
    }
  },
  created() {
    firebase.auth().onAuthStateChanged(user => {
      if (user) {
        this.id = user.uid;
        this.ready = true;
      } else {
        //TODO
        this.id = "";
      }
    });
  },
  beforeRouteEnter(to, from, next) {
    next(vm => {
      firebase.auth().onAuthStateChanged(user => {
        if (user) {
          next();
        } else {
          vm.$router.push("/login");
        }
      });
    });
  }
};
</script>

<style></style>
