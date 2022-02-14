<template>
  <div class="line-head" v-if="selected_variant">{{selected_variant.patch.slice(-1)[0].diff.slice(1,-1).split(',').slice(0, 5).join()}}</div>
  <pre
      v-if="selected_variant && selected_variant.patch.slice(-1)[0].diff !== null"
      class="prettyprint linenums source-code prettyprinted code_width"
      id="diff"
  >
    <ol class="linenums">
      <li
          v-if="selected_variant.patch.slice(-1)[0].diff"
          v-for="(code_line, index) in selected_variant.patch.slice(-1)[0].diff.slice(1,-1).split(',').slice(5)"
          class="line"
          :class="'L'+(index+1)"
          :style="
          {
            background: 'rgba('+(code_line[1] === '-' ? 255 : 0)+','+(code_line[1] === '+' ? 255 : 0)+',0,'+(code_line[1] === '+' || code_line[1] === '-' ? 0.6 : 0.0)+')'
          }"
      >{{code_line}}</li>
    </ol>
  </pre>
  <template v-else>
    <img alt="Vue logo" src="../../assets/kgenprog-logo.png" width="200" height="200">
    <h3>Unable to display diff</h3>
  </template>
</template>

<script>
export default {
  name: "DiffView",
  props: ["selected_variant"]
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