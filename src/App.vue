<template>
  <div id="app">
    <h1>Búsqueda de Equipos</h1>
    <SearchBar />
  </div>
</template>

<script>
import SearchBar from "./components/SearchBar.vue";

export default {
  components: {
    SearchBar,
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  text-align: center;
  background-color: #f9f9f9;
}

#app {
  max-width: 600px;
  margin: 20px auto;
  padding: 20px;
  background: white;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
}
</style>
