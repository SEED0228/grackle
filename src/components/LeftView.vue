<template>
  <div class="row">
    <div class="col-2 offset-1">
      <button
          class="btn btn-success col-12 mt-2 mb-2"
          @click="show_table = false; show_tree = true;"
      >
        Tree
      </button>
      <button
          class="btn btn-primary col-12 mt-2 mb-2"
          @click="show_table = true; show_tree = false;"
      >
        Table
      </button>
    </div>
    <div class="col-2 offset-1">
      <button
          v-if="show_tree"
          class="btn btn-outline-success col-12 mt-2 mb-2"
          @click="show_suspicious= true;"
      >
        Suspicious
      </button>
      <button
          v-if="show_tree"
          class="btn btn-outline-primary col-12 mt-2 mb-2"
          @click="show_suspicious= false;"
      >
        Operation
      </button>
    </div>
    <div class="col-6" v-if="show_tree">
      <h4>Tree Size</h4>
      <div class="row">
        <input v-model="node_size" type="range" id="volume" name="volume" min="20" max="80" step="10">
      </div>
      <label for="volume">{{node_size}}</label>
    </div>
  </div>
  <div class="row overflow-auto var-table" v-if="show_table">
    <VariantTable
        :history=history
        :selected_variant_id=selected_variant_id
        @updateSelectedVariantId="$emit('updateSelectedVariantId', $event)"
        v-if="renderComponent"
    />
  </div>
  <div
      class="row var-table"
      style="overflow-x: scroll; overflow-y: scroll;display:block;"
      v-if="show_tree"
  >
    <VariantTree
        :show_suspicious=show_suspicious
        :history=history
        :selected_variant_id=selected_variant_id
        :node_size=node_size
        @updateSelectedVariantId="$emit('updateSelectedVariantId', $event)"
        v-if="renderComponent"
    />
  </div>
</template>

<script>
import VariantTable from "./left_view_components/VariantTable";
import VariantTree from "./left_view_components/VariantTree";

export default {
  name: "LeftView",
  props: ['history', 'selected_variant_id', 'updateSelectedVariantId'],
  components: {
    VariantTable,
    VariantTree
  },
  data() {
    return {
      show_table: false,
      show_tree: true,
      show_suspicious: false,
      node_size: 50,
      renderComponent: true
    }
  },
  methods: {
    forceRerender() {
      // Removing my-component from the DOM
      this.renderComponent = false;

      this.$nextTick(() => {
        // Adding the component back in
        this.renderComponent = true;
      });
    }
  },
  watch: {
    history: function (){
      this.forceRerender();
    }
  }
}
</script>

<style scoped>
  .var-table {
    height: 80vh;
  }
</style>
