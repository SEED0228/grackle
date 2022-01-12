<template>
  <h1>{{selected_variant_id}}</h1>
  <pre v-if="sourceCode !== null" class="prettyprint linenums source-code prettyprinted" id="code"><ol class="linenums"><li v-for="(code_line, index) in sourceCode.split('\n')" class="line" :class="'L'+(index+1)" :style="{background: 'rgba('+255+',0,0,'+suspiciousness_values[index+1]**3*0.8+')'}">{{code_line}}</li></ol></pre>
  <h3 v-else="sourceCode !== null">ソースコードを表示できません</h3>
</template>

<script>
export default {
  name: "SuspiciousView",
  props: ['history', 'selected_variant_id'],
  methods: {
    load_file: function() {
      const selected_variant = this.history.variants[this.selected_variant_id];
      this.sourceCode = selected_variant.sourceCode === "NaN" ? null : selected_variant.sourceCode;
      this.suspiciousness_values = [];
      if(this.sourceCode !== null) {
        this.update_suspiciousness_values(selected_variant);
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
    }
  },
  data() {
    return {
      sourceCode: null,
      suspiciousness_values: [] //行ごとの最大疑惑値
    }
  },
  watch: {
    selected_variant_id: function(newVal, oldVal) {
      // console.log(newVal, oldVal);
      this.load_file();
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
</style>