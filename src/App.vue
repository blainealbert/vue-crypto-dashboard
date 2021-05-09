
<template>
  <div id="app">
    <Nav />
    <Coins />
  </div>
</template>

<script>
import Coins from "./components/Coins";
import Nav from "./components/Nav";

export default {
  name: "App",
  components: {
    Coins,
    Nav,
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Rubik:ital,wght@0,300;0,400;0,500;0,600;0,700;1,300;1,400;1,500;1,600;1,700&display=swap");
#app {
  font-family: "Rubik", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #202223;
  font-size: 16px;
}
html {
  background-color: #f5f5f5;
}
body{
  margin: 0;
}
@media screen and (min-width: 768px) {
  #app {
  }
}
</style>
