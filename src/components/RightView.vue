<template>
  <div class="row">
    <div class="col-10 offset-1 mt-3 mb-3">
      <label for="formFileLg" class="form-label">Input the json file output from kgenprog</label>
      <input class="form-control form-control-lg" id="formFileLg" type="file" ref="file" @change="loadHistory" >
    </div>
  </div>
  <template v-if="selected_variant">
    <h1>{{selected_variant_id}}</h1>
    <select class="form-select my-3" v-model="selected_code_option">
      <option v-for="option in code_options"
              v-bind:value="option.id"
              v-bind:key="option.id">
        {{ option.name }}
      </option>
    </select>
    <SourceCodeView v-if="selected_code_option === 1" :selected_variant="selected_variant" :history="history"></SourceCodeView>
    <DiffView v-else-if="selected_code_option === 2" :selected_variant="selected_variant"></DiffView>
    <select
        class="form-select my-5"
        v-model="selected_table_option"
    >
      <option v-for="option in table_options"
              v-bind:value="option.id"
              v-bind:key="option.id"
      >{{ option.name }}</option>
    </select>
    <SuspiciousnessTable v-if="selected_table_option === 1" :selected_variant="selected_variant"></SuspiciousnessTable>
    <TestResultView v-if="selected_table_option === 2" :selected_variant="selected_variant"></TestResultView>
  </template>
  <template v-else>
    <img alt="Vue logo" src="../assets/kgenprog-logo.png" width="200" height="200">
    <h3><strong>Grackle</strong> is a tool that visualizes the automatic program modification process of <strong>kGenProg</strong> in a tree structure.
      You can get more information about the variant by clicking on the <strong>nodes</strong> in the tree.</h3>
  </template>
</template>

<script>
import SourceCodeView from "./right_view_components/SourceCodeView";
import DiffView from "./right_view_components/DiffView";
import SuspiciousnessTable from "./right_view_components/SuspiciousnessTable";
import TestResultView from "./right_view_components/TestResultView";
export default {
  name: "RightView",
  components: {TestResultView, SuspiciousnessTable, DiffView, SourceCodeView},
  props: ['history', 'selected_variant_id'],
  methods: {
    loadVariantInformation: function() {
      this.selected_variant = this.history.variants[this.selected_variant_id];
      if(this.selected_variant) {
        this.sourceCode = this.selected_variant.sourceCode === "NaN" ? null : this.selected_variant.sourceCode;
        this.suspiciousness_values = [];
      }
      if(this.sourceCode !== null) {
        this.update_suspiciousness_values(this.selected_variant);
      }
    },
    update_suspiciousness_values: function(selected_variant) {
      this.suspiciousness_values = Array(this.sourceCode.split('\n').length).fill(0.0);
      for(let suspiciousness of selected_variant.suspiciousnesses) {
        for(let i = suspiciousness.lineNumberRange.start; i <= suspiciousness.lineNumberRange.end; i++) {
          if(this.suspiciousness_values[i] < suspiciousness.value) {
            this.suspiciousness_values[i] = suspiciousness.value;
          }
        }
      }
    },
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
    async loadHistory(event) {
      const files = event.target.files || event.dataTransfer.files
      const file = files[0]

      if (!this.checkFile(file)) {
        alert("ファイルを読み込めませんでした")
        return
      }

      const logData = await this.getFileData(file)
      this.$emit('updateHistory', JSON.parse(logData))
    }
  },
  data() {
    return {
      sourceCode: null,
      selected_variant: null,
      selected_table_option: 1,
      table_options: [
        {id: 1, name: "suspiciousness"},
        {id: 2, name: "test results"}
      ],
      selected_code_option: 1,
      code_options: [
        {id: 1, name: "suspiciousness"},
        {id: 2, name: "difference"}
      ],
      suspiciousness_values: [] //行ごとの最大疑惑値
    }
  },
  watch: {
    selected_variant_id: function(newVal, oldVal) {
      this.loadVariantInformation();
    },
    history: function(newVal, oldVal) {
      this.sourceCode = null;
      this.selected_variant = null;
      this.suspiciousness_values = [];
    }
  }
}
</script>

<style scoped>
</style>