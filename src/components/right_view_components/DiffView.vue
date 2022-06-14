<template>
  <template v-if="selected_variant.id !== 0 ? selected_variant.patch.slice(-1)[0].diff : null !== null">
    <div class="code_width" v-html="prettyHtml"></div>
  </template>
  <template v-else>
    <img alt="Vue logo" src="../../assets/kgenprog-logo.png" width="200" height="200">
    <h3>Unable to display diff</h3>
  </template>
</template>

<script>
import * as Diff2Html from 'diff2html';
import 'diff2html/bundles/css/diff2html.min.css';
export default {
  name: "DiffView",
  props: ["selected_variant"],
  computed: {
    prettyHtml() {
      let diffs = "";
      for(let patch of this.selected_variant.patch) {
        let fixed_diff = patch.diff.replace(/@@ -(\d+),(\d+) \+(\d+),(\d+) @@/g, '@@ -$1, $2 +$3, $4 @@')
        diffs += fixed_diff.slice(1,-1).split(',')[0] + "\n";
        diffs += fixed_diff.slice(1,-1).split(',')[1].slice(1) + "\n";
        diffs += fixed_diff.slice(1,-1).split(',').slice(2).map(line => line.slice(1)).join("\n")+"\n";
      }
      diffs = diffs.replace(/@@ -(\d+)\n(\d+) \+(\d+)\n(\d+) @@/g, '@@ -$1,$2 +$3,$4 @@');
      return Diff2Html.html(diffs, {
        drawFileList: false,
        matching: 'lines',
        outputFormat: 'line-by-line',
      });
    },
  },
}
</script>

<style scoped>

</style>
