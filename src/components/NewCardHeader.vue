<template>
  <div class="row justify-content-between">
    <h2 class="ml-4">{{ headerText }}</h2>
    <gb-button
      v-if="button"
      :color="getColor()"
      @click="$emit('click', buttonState)"
      :left-icon="getIcon()"
      class="mr-4"
    >
      {{ getText() }}
    </gb-button>
  </div>
</template>

<script>
export default {
  name: "NewCardHeader",
  props: {
    headerText: "",
    buttonState: "", //"add" or "cancel"
    button: Boolean
  },
  methods: {
    getColor() {
      if (this.buttonState === "cancel") {
        return "red";
      } else {
        //add
        return "blue";
      }
    },
    getIcon() {
      if (this.buttonState === "cancel") {
        return "close";
      } else {
        //add
        return "add";
      }
    },
    getText() {
      if (this.buttonState === "cancel") {
        //this.buttonText = "Cancel"
        return "Cancel";
      } else {
        //add
        return "New Card";
      }
    }
  }
};
</script>
