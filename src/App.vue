<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-6">
        <div class="row">
          <button class="btn btn-success col-2 offset-3 mt-3 mb-3" @click="show_table = false; show_tree = true;">Tree</button>
          <button class="btn btn-primary col-2 offset-1 mt-3 mb-3" @click="show_table = true; show_tree = false;">Table</button>
          <button v-if="show_tree" class="btn btn-outline-success col-2 offset-3 mt-3 mb-3" @click="show_suspicious= true;">Suspicious</button>
          <button v-if="show_tree" class="btn btn-outline-primary col-2 offset-1 mt-3 mb-3" @click="show_suspicious= false;">Operation</button>
        </div>
        <div class="row overflow-auto var-table" v-if="show_table">
          <VariantTable :history=history :selected_variant_id=selected_variant_id @updateSelectedId="selected_variant_id = $event"/>
        </div>
        <div class="row var-table" style="overflow-x: scroll; overflow-y: scroll;display:block;" v-if="show_tree">
          <VariantTree :show_suspicious=show_suspicious :history=history :selected_variant_id=selected_variant_id @updateSelectedId="selected_variant_id = $event"/>
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
import VariantTree from "./components/VariantTree"
import SuspiciousView from "./components/SuspiciousView";

export default {
  name: 'App',
  components: {
    SuspiciousView,
    HelloWorld,
    VariantTable,
    VariantTree
  },
  data() {
    return {
      history: history,
      selected_variant_id: null,
      show_table: false,
      show_tree: true,
      show_suspicious: false
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
