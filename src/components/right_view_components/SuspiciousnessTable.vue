<template>
  <table
      class="table table-striped my-5"
  >
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
</template>

<script>
export default {
  name: "SuspiciousnessTable",
  props: ['selected_variant']
}
</script>

<style scoped>

</style>