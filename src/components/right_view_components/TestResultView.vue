<template>
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

<script>
export default {
  name: "TestResultView",
  props: ["selected_variant"]
}
</script>

<style scoped>

</style>