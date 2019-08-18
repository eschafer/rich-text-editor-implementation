<template>
  <div id="app">
    <HelloWorld msg="Hello Vue in CodeSandbox!"/>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",
  components: {
    HelloWorld
  }
};
</script>

<style>
#app {
  font-family: system-ui, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  border: 1px solid black;
  padding: 0 1em;
}
</style>
