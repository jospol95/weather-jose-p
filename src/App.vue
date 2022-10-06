<template>
  <MainTemplateComponent></MainTemplateComponent>
</template>

<script>
import MainTemplateComponent from "./components/MainTemplateComponent.vue";

export default {
  name: "App",
  components: {
    MainTemplateComponent,
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Open+Sans&display=swap");
#app {
  font-family: "Open Sans", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
