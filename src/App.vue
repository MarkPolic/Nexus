<template>
  <div id="app">
    <VehicleSelector />
  </div>
</template>

<script>
import VehicleSelector from "./components/VehicleSelector.vue";

export default {
  name: "App",
  components: {
    VehicleSelector,
  },
};
</script>
<style lang="scss">
.page_wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: start;
  height: 100vh;
  background-color: #f5f5f5; /* Light gray background */
}
body {
  margin: 0px;
}
/* Styling for the main heading */
h1 {
  font-size: 32px;
  color: #333;
  font-weight: 600;
  margin-bottom: 30px;
  text-align: center;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-family: monospace;
}
</style>
