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
              background: 'rgba('+255+',0,0,'+suspiciousness_values[index+1]**3*0.8+')'
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
  props: ["selected_variant_id", "history"],
  data() {
    return {
      sourceCode: null,
      selected_variant: null,
      suspiciousness_values: [] //行ごとの最大疑惑値
    }
  },
  methods: {
    loadVariantInformation: function () {
      this.selected_variant = this.history.variants[this.selected_variant_id];
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
      this.selected_variant = null;
      this.suspiciousness_values = [];
      console.log(this.sourceCode)
    },
    selected_variant_id: function(newVal, oldVal) {
      this.loadVariantInformation();
    }
  }
}
</script>

<style scoped>

</style>