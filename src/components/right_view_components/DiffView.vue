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
      const last_patch = this.selected_variant.patch.slice(-1)[0];
      let diffs = last_patch.diff.slice(1,-1).split(',')[0] + "\n"
      diffs += last_patch.diff.slice(1,-1).split(',')[1].slice(1) + "\n"
      diffs += last_patch.diff.slice(1,-1).split(',').slice(2, 5).join().slice(1) + "\n"
      diffs += last_patch.diff.slice(1,-1).split(',').slice(5).map(line => line.slice(1)).join("\n");
      console.log(diffs);
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
