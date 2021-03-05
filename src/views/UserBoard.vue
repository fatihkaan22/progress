<template>
  <div id="app">
    <div class="container">
      <NewCardHeader :headerText="displayName" :button="false" />
      <gb-divider />
      <gb-spinner v-if="loading" class="center-in-page p-4 m-5" />
      <p v-if="list.length == 0 && !loading">
				Couldn't find any cards to show.
      </p>
      <div class="row justify-content-center mx-auto">
        <div v-for="i in list" class="d-inline">
          <ProgressCard :editable="false" v-bind:key="i.id" :card="i" />
        </div>
        <div v-for="index in 2" :key="index" class="hidden d-inline" />
      </div>
    </div>
  </div>
</template>

<script>
import ProgressCard from "@/components/CircularProgress.vue";
import NewCardHeader from "@/components/NewCardHeader.vue";

export default {
  name: "User",
  components: {
    ProgressCard,
    NewCardHeader
  },
  data() {
    return {
      list: [],
      loading: true,
      displayName: "",
      userUid: ""
    };
  },
  methods: {
    setDisplayName() {
      db.ref("users")
        .child(this.userUid)
        .child("name")
        .get()
        .then(snapshot => {
          this.displayName = snapshot.val();
        });
    },
    fetchData() {
      var listRef = db.ref("users/" + this.userUid + "/list");
      listRef.on("value", snapshot => {
        let list = [];
        let i = 0;
        snapshot.forEach(function(childSnapshot) {
          var item = childSnapshot.val();
          item.id = childSnapshot.key;
          list.push(item);
        });
        this.list = list.reverse();
        this.loading = false; // hide spinner
      });
    }
  },
  created() {
    const urlParams = new URLSearchParams(window.location.search);
    this.userUid = urlParams.get("id");
    //    console.log(urlParams);
    //   console.log(this.userUid);
    this.setDisplayName();
    this.fetchData();
  }
};
</script>

<style scoped>
.hidden {
  width: 19rem;
  margin: 1rem;
}
</style>
