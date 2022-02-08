<template>
  <div id="app">
    <div class="container">
      <SupplyTable />
    </div>
  </div>
</template>

<script>
import SupplyTable from "@/components/SupplyTable";
export default {
  name: "App",
  components: {
    SupplyTable,
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.container {
  max-width: 1200px;
  margin: 20px auto;
  display: flex;
  width: 100%;
  flex-direction: column;
}
</style>
