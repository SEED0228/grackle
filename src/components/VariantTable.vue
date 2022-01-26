<template>
  <table class="table">
    <tr>
      <th>id</th>
      <th>generationNumber</th>
      <th>fitness</th>
      <th>suspiciousnesses</th>
      <td>bases</td>
    </tr>
    <tr  v-if="history" v-for="variant in history.variants" :id="variant.id" v-bind:class="{'table-success':variant.id===selected_variant_id, 'table-danger':variant.id!==selected_variant_id && parent_variant_ids.includes(variant.id)}" @click="select(variant.id)">
      <td>{{ variant.id }}</td>
      <td>{{variant.generationNumber}}</td>
      <td>{{ variant.fitness }}</td>
      <td><span v-for="sus in variant.suspiciousnesses">({{sus.value}}, {{sus.lineNumberRange.start}}, {{sus.lineNumberRange.end}})</span></td>
      <td><span v-for="base in variant.bases">({{base.name}}, {{base.lineNumberRange.start}}, {{base.lineNumberRange.end}})</span></td>
    </tr>
  </table>
</template>

<script>

export default {
  name: "VariantTable",
  props: ['history', 'selected_variant_id', 'updateSelectedId'],
  data() {
    return {
      parent_variant_ids: []
    }
  },
  methods: {
    select: function(id) {
      this.parent_variant_ids = [];
      this.$emit('updateSelectedId', id);
      const stack = [];
      stack.push(id);
      var x, ids;
      while(stack.length > 0) {
        x = stack.pop();
        this.parent_variant_ids.push(x);
        ids = this.history.variants[x].operation.parentIds;
        ids.forEach(function( id ) {
          // console.log( id );
          stack.push(id);
        });
      }
    }
  }
}
</script>

<style scoped>

</style>