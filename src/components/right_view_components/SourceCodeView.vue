<template>
  <pre
      v-if="sourceCode !== null"
      class="prettyprint linenums source-code prettyprinted code_width"
      id="code"
  >
      <ol class="linenums">
        <li
            v-if="sourceCode"
            v-for="(code_line, index) in sourceCode.split('\n')" class="line" :class="'L'+(index+1)"
            :style="
            {
              background: 'rgba('+255+',0,0,'+suspiciousness_values[index+1]**2+')'
            }"
        >{{code_line}}</li>
      </ol>
    </pre>
  <template v-else>
    <img alt="Vue logo" src="../../assets/kgenprog-logo.png" width="200" height="200">
    <h3>Unable to view source code</h3>
  </template>
</template>

<script>
export default {
  name: "SourceCodeView",
  props: ["selected_variant", "history"],
  data() {
    return {
      sourceCode: null,
      suspiciousness_values: [] //行ごとの最大疑惑値
    }
  },
  created: function () {
    this.loadVariantInformation();
  },
  methods: {
    loadVariantInformation: function () {
      if (this.selected_variant) {
        this.sourceCode = this.selected_variant.sourceCode === "NaN" ? null : this.selected_variant.sourceCode;
        this.suspiciousness_values = [];
      }
      if (this.sourceCode !== null) {
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
    }
  },
  watch: {
    history: function(){
      this.sourceCode = null;
      this.suspiciousness_values = [];
    },
    selected_variant: function() {
      this.loadVariantInformation();
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
  height: 60vh;
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
</style>
