<template>
  <MainTemplateComponent></MainTemplateComponent>
</template>

<script>
// import HelloWorld from "./components/HelloWorld.vue";
// import WeatherMainComponent from "./components/WeatherMainComponent.vue";
import MainTemplateComponent from "./components/MainTemplateComponent.vue";

export default {
  name: "App",
  components: {
    // WeatherMainComponent,
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
  /*text-align: center;*/
  /*background-color: red;*/
  /*width: 50%;*/
  /*color: #2c3e50;*/
  /*margin-top: 60px;*/
}
</style>
