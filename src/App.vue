<template>
  <div id="app">
    <Comparar />
  </div>
</template>

<script>
import Comparar from "./components/Comparar";

export default {
  name: "App",
  components: {
    Comparar,
  },
};
</script>

<style>
body, html {
  padding: 0;
  margin: 0;
  width: 100%;
  height: 100%;
  background-color: #F2F4FF;
}
</style>