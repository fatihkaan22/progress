<template>
  <div id="app">
    <div class="container">
      <NewCardHeader
        @click="newCardClicked"
        headerText="Your Progress"
				:button="true"
        :buttonState="buttonState"
      />
      <gb-divider />
			<gb-spinner v-if="loading" class="center-in-page p-4 m-5"/>
      <div class="row justify-content-center mx-auto">
        <NewProgress
          @value="addNewProgressDone"
          :card="newCardTemplate"
          v-if="onNewCard"
        />
        <div v-for="i in list" class="d-inline">
          <ProgressCard
						:editable="true"
            v-bind:key="i.id"
            @delete="deleteCard"
            @edit="editCard"
            :card="i"
          />
        </div>
        <div v-for="index in 2" :key="index" class="hidden d-inline" />
      </div>
    </div>
  </div>
</template>

<script>
import NewProgress from "@/components/NewProgress.vue";
import NewCardHeader from "@/components/NewCardHeader.vue";
import ProgressCard from "@/components/CircularProgress.vue";
import firebase from "firebase";

export default {
  name: "Progress",
  components: {
    ProgressCard,
    NewProgress,
    NewCardHeader,
  },
  data() {
    return {
      list: [],
			loading: true,
      authUser: { displayName: "Your Progress" },
      onNewCard: false,
      editingCard: {},
      newCardTemplate: {},
    };
  },
  methods: {
    newCardClicked(state) {
      //console.log("editingcard", this.editingCard.id);
      if (this.editingCard.id) {
        this.addToDB(this.editingCard);
      }
      this.newCardTemplate = {};
      this.changeButtonState();
    },
    editCard(card) {
      this.editingCard = { ...card };
      this.deleteCard(card.id);
      this.newCardTemplate = card;
      this.changeButtonState();
    },
    addToDB(item) {
      if (!this.authUser) return;
      var ref;
      if (!item.id) {
        //console.log("addtodb", item.id);
        ref = db.ref("users/" + this.authUser.uid + "/list").push();
      } else {
        ref = db.ref("users/" + this.authUser.uid + "/list/" + item.id);
      }
      item.id = null;
      ref
        .set(item)
        .then(() => {
          //console.log("Success");
        })
        .catch(() => {
          console.error("Error");
        });
    },
    deleteCard(id) {
      var cardRef = db.ref("users/" + this.authUser.uid + "/list/" + id);
      cardRef
        .set({})
        .then(() => {
          //console.log("Success");
        })
        .catch(() => {
          console.error("Error");
        });

      //console.log(id);
    },
    addNewProgressDone(item) {
      this.addToDB(item);
      this.changeButtonState();
    },
    changeButtonState() {
      if (this.onNewCard) {
        this.buttonState = "add";
      } else {
        this.buttonState = "cancel";
      }
      this.onNewCard = !this.onNewCard;
    },
    fetchData() {
      var listRef = db.ref("users/" + this.authUser.uid + "/list");
      listRef.on("value", snapshot => {
        let list = [];
        let i = 0;
        snapshot.forEach(function(childSnapshot) {
          var item = childSnapshot.val();
          item.id = childSnapshot.key;
          list.push(item);
        });
        this.list = list.reverse();
				this.loading = false;
      });
    }
  },
  created() {
    this.buttonState = "add";
    firebase.auth().onAuthStateChanged(user => {
      if (user) {
        this.authUser = user;
        //console.log(user.email); // FIXME
        this.fetchData();
      } else {
        //TODO
        this.authUser = {};
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

<style scoped="">
h2 {
  color: white;
}
.hidden {
  width: 19rem;
  margin: 1rem;
}
</style>
