<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-8">
        <LeftView
            :selected_variant_id=selected_variant_id
            :history=history
            @updateSelectedVariantId="selected_variant_id = $event"
            v-if="renderComponent"
        />
      </div>
      <div class="col-4">
        <RightView
            :history=history
            :selected_variant_id=selected_variant_id
            @updateHistory="updateHistory"
            v-if="renderComponent"
        />
      </div>
    </div>
  </div>
</template>

<script>
import history from './assets/kgenprog-out/history.json'
import RightView from "./components/RightView";
import LeftView from "./components/LeftView";

export default {
  name: 'App',
  components: {
    LeftView,
    RightView
  },
  data() {
    return {
      history: history,
      selected_variant_id: null,
      renderComponent: true
    }
  },
  methods: {
    updateHistory(history) {
      this.history =  history
      this.selected_variant_id = null
      this.forceRerender
    },
    forceRerender() {
      // Removing my-component from the DOM
      this.renderComponent = false;

      this.$nextTick(() => {
        // Adding the component back in
        this.renderComponent = true;
      });
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
