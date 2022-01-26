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
        <div class="row overflow-auto var-table" v-if="show_table && renderComponent">
          <VariantTable :history=history :selected_variant_id=selected_variant_id @updateSelectedId="selected_variant_id = $event"/>
        </div>
        <div class="row var-table" style="overflow-x: scroll; overflow-y: scroll;display:block;" v-if="show_tree && renderComponent">
          <VariantTree :show_suspicious=show_suspicious :history=history :selected_variant_id=selected_variant_id @updateSelectedId="selected_variant_id = $event"/>
        </div>
      </div>
      <div class="col-6">
        <div class="row">
          <div class="col-10 offset-1 mt-3 mb-3">
            <label for="formFileLg" class="form-label">kgenprogから出力されたjsonファイルを入力</label>
            <input class="form-control form-control-lg" id="formFileLg" type="file" ref="file" @change="load_history" >
          </div>
        </div>
        <img alt="Vue logo" src="./assets/kgenprog-logo.png" width="200" height="200">
<!--        <HelloWorld msg="Welcome to Your Vue.js App"/>-->
        <SuspiciousView v-if="renderComponent" :history=history :selected_variant_id=selected_variant_id />
      </div>
    </div>
  </div>
</template>

<script>
import history from './assets/kgenprog-out/history.json'
import VariantTable from "./components/VariantTable"
import VariantTree from "./components/VariantTree"
import SuspiciousView from "./components/SuspiciousView";

export default {
  name: 'App',
  components: {
    SuspiciousView,
    VariantTable,
    VariantTree
  },
  data() {
    return {
      history: history,
      selected_variant_id: null,
      renderComponent: true,
      show_table: false,
      show_tree: true,
      show_suspicious: false
    }
  },
  methods: {
    getFileData(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader()
        reader.readAsText(file)
        reader.onload = () => resolve(reader.result)
        reader.onerror = error => reject(error)
      })
    },

    checkFile(file) {
      if (!file) {
        return false
      }

      if (file.type !== 'application/json') {
        return false
      }

      const SIZE_LIMIT = 5000000 // 5MB
      if (file.size > SIZE_LIMIT) {
        return false
      }
      return true
    },
    async load_history(event) {
      const files = event.target.files || event.dataTransfer.files
      const file = files[0]

      if (!this.checkFile(file)) {
        alert("ファイルを読み込めませんでした")
        return
      }

      const logData = await this.getFileData(file)

      this.history =  JSON.parse(logData)

      // console.log(this.history)
      this.forceRerender
    }
  },
  forceRerender() {
    // Removing my-component from the DOM
    this.renderComponent = false;

    this.$nextTick(() => {
      // Adding the component back in
      this.renderComponent = true;
    });
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
