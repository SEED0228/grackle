<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-6">
        <div class="row overflow-auto var-table">
          <VariantTable :history=history :selected_variant_id=selected_variant_id @updateSelectedId="selected_variant_id = $event"/>
        </div>
      </div>
      <div class="col-6">
        <img alt="Vue logo" src="./assets/logo.png">
<!--        <HelloWorld msg="Welcome to Your Vue.js App"/>-->
        <SuspiciousView :history=history :selected_variant_id=selected_variant_id />
      </div>
    </div>
  </div>
</template>

<script>
import history from './assets/kgenprog-out/history.json'
import HelloWorld from './components/HelloWorld.vue'
import VariantTable from "./components/VariantTable"
import SuspiciousView from "./components/SuspiciousView";

export default {
  name: 'App',
  components: {
    SuspiciousView,
    HelloWorld,
    VariantTable
  },
  data() {
    return {
      history: history,
      selected_variant_id: null
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.var-table {
  height: 80vh;
}
</style>
