<template>
  <div class="row">
    <div class="col-10 offset-1 mt-3 mb-3">
      <label for="formFileLg" class="form-label">Input the json file output from kgenprog</label>
      <input class="form-control form-control-lg" id="formFileLg" type="file" ref="file" @change="loadHistory" >
    </div>
  </div>
  <h1>{{selected_variant_id}}</h1>
  <select class="form-select my-3" v-model="selected_code_option" v-if="selected_variant">
    <option v-for="option in code_options"
            v-bind:value="option.id"
            v-bind:key="option.id">
      {{ option.name }}
    </option>
  </select>
  <template v-if="selected_code_option === 1">
    <pre v-if="sourceCode !== null" class="prettyprint linenums source-code prettyprinted code_width" id="code"><ol class="linenums"><li v-if="sourceCode" v-for="(code_line, index) in sourceCode.split('\n')" class="line" :class="'L'+(index+1)" :style="{background: 'rgba('+255+',0,0,'+suspiciousness_values[index+1]**3*0.8+')'}">{{code_line}}</li></ol></pre>
    <template v-else>
      <img alt="Vue logo" src="../assets/kgenprog-logo.png" width="200" height="200">
      <h3>Unable to view source code</h3>
    </template>
  </template>
  <template v-else-if="selected_code_option === 2">
    <div class="line-head">{{selected_variant.patch.slice(-1)[0].diff.slice(1,-1).split(',').slice(0, 5).join()}}</div>
    <pre v-if="selected_variant.patch.slice(-1)[0].diff !== null" class="prettyprint linenums source-code prettyprinted code_width" id="diff">
      <ol class="linenums">
        <li v-if="selected_variant.patch.slice(-1)[0].diff" v-for="(code_line, index) in selected_variant.patch.slice(-1)[0].diff.slice(1,-1).split(',').slice(5)" class="line" :class="'L'+(index+1)" :style="{background: 'rgba('+(code_line[1] === '-' ? 255 : 0)+','+(code_line[1] === '+' ? 255 : 0)+',0,'+(code_line[1] === '+' || code_line[1] === '-' ? 0.6 : 0.0)+')'}">{{code_line}}</li>
      </ol>
    </pre>
    <template v-else>
      <img alt="Vue logo" src="../assets/kgenprog-logo.png" width="200" height="200">
      <h3>Unable to display diff</h3>
    </template>
  </template>
  <select class="form-select my-5" v-model="selected_table_option" v-if="selected_variant">
    <option v-for="option in table_options"
            v-bind:value="option.id"
            v-bind:key="option.id">
      {{ option.name }}
    </option>
  </select>
  <table v-if="selected_variant && selected_table_option === 1" class="table table-striped my-5">
    <thead>
      <tr>
        <th>value</th>
        <th>from</th>
        <th>to</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="suspiciousness in selected_variant.suspiciousnesses">
        <td>{{suspiciousness.value}}</td>
        <td>{{suspiciousness.lineNumberRange.start}}</td>
        <td>{{suspiciousness.lineNumberRange.end}}</td>
      </tr>
    </tbody>
  </table>
  <template v-if="selected_variant && selected_table_option === 2">
    <div v-if="selected_variant" class="row mx-2 my-5">
      <div class="col-3 border border-dark">
        <p class="fs-2 mt-1">{{selected_variant.testSummary.testResults.length}}</p>
        <p class="fs-6">tests</p>
      </div>
      <div class="col-3 border border-dark">
        <p class="fs-2 mt-1">{{selected_variant.testSummary.testResults.filter((v) => v.isSuccess).length}}</p>
        <p class="fs-6">successes</p>
      </div>
      <div class="col-3 border border-dark">
        <p class="fs-2 mt-1">{{selected_variant.testSummary.testResults.filter((v) => !v.isSuccess).length}}</p>
        <p class="fs-6">failures</p>
      </div>
      <div class="col-3 border border-dark">
        <p class="fs-2 mt-1">{{Math.round(selected_variant.testSummary.successRate * 100) / 100}}</p>
        <p class="fs-6">rate</p>
      </div>
    </div>
    <table v-if="selected_variant" class="table table-striped">
      <thead>
      <tr>
        <th>fqn</th>
        <th>is success?</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="result in selected_variant.testSummary.testResults">
        <td>{{result.fqn}}</td>
        <td>{{result.isSuccess}}</td>
      </tr>
      </tbody>
    </table>
  </template>
</template>

<script>
export default {
  name: "RightView",
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
  #code {
    max-height: 700px;
    overflow-x: scroll;
    overflow-y: scroll;
    border: 1px solid #ccc;
    text-align: left;
  }

  .code_width {
    height: 35vh;
  }

  #diff {
    max-height: 700px;
    overflow-x: scroll;
    overflow-y: scroll;
    border: 1px solid #ccc;
    text-align: left;
  }

  pre.prettyprint {
    padding: 2px;
    border: 1px solid #888;
  }

  ol.linenums {
    margin-top: 0;
    margin-bottom: 0;
  }

  .line::marker {
    unicode-bidi: isolate;
    font-variant-numeric: tabular-nums;
    text-transform: none;
    text-indent: 0px !important;
    text-align: start !important;
    text-align-last: start !important;
  }

  .line-head {
    background-color: #a0a0a0;
    text-align: left;
  }
</style>