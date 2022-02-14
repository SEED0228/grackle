<template>
  <div
      v-for="(variants, genNum) in successful_build_variant_information"
      class="col-12"
      :style="
      {
        height: (genNum === 0 ? Math.round(node_size) : Math.round(node_size)*4)+'px'
      }"
  >
    <div
        :style="'text-align: left; margin-left: '+Math.round(node_size)*2+'px;'"
        v-if="0 < genNum"
    >
      <svg
          version="1.1"
          xmlns="http://www.w3.org/2000/svg"
          :height=Math.round(node_size)*3
      >
        <template
            v-for="(variant) in variants.filter((v) => v.is_copied == true)"
        >
          <line
              v-if="show_suspicious"
              class="line"
              :x1="Math.round(node_size)+successful_build_variant_information[genNum-1].find((v) => v.id === variant.id).index*Math.round(node_size)*2"
              y1="0"
              :x2="Math.round(node_size)+variant.index*Math.round(node_size)*2"
              :y2="Math.round(node_size)*3"
              stroke="black"
              opacity="0.5"
          />
          <line
              v-else
              class="line"
              :x1="Math.round(node_size)+successful_build_variant_information[genNum-1].find((v) => v.id === variant.id).index*Math.round(node_size)*2"
              y1="0"
              :x2="Math.round(node_size)+variant.index*Math.round(node_size)*2"
              :y2="Math.round(node_size)*3"
              stroke="black"
          />
        </template>
        <template
            v-for="(variant) in variants.filter((v) => v.is_copied == false)"
        >
          <template
              v-for="id in history.variants[variant.id].operation.parentIds"
          >
            <line
                v-if="show_suspicious && variant.operation_name !== 'random-crossover'"
                class="line"
                :x1="Math.round(node_size)+successful_build_variant_information[genNum-1].find((v) => v.id === id).index*Math.round(node_size)*2"
                y1="0"
                :x2="Math.round(node_size)+variant.index*Math.round(node_size)*2"
                :y2="Math.round(node_size)*3"
                stroke="red"
                :opacity="variant.max_suspicious_value**3"
            />
            <line
                v-else-if="show_suspicious && variant.operation_name === 'random-crossover'"
                class="line" :x1="Math.round(node_size)+successful_build_variant_information[genNum-1].find((v) => v.id === id).index*Math.round(node_size)*2"
                y1="0"
                :x2="Math.round(node_size)+variant.index*Math.round(node_size)*2"
                :y2="Math.round(node_size)*3"
                stroke="black"
            />
            <line
                v-else
                class="line"
                :class="
                {
                  'insert-line': (variant.operation_name === 'insert_after' || variant.operation_name === 'insert_before'),
                  'replace-line': (variant.operation_name === 'replace'), 'cross-line': (variant.operation_name === 'random-crossover'),
                  'delete-line': (variant.operation_name === 'delete')
                }"
                :x1="Math.round(node_size)+successful_build_variant_information[genNum-1].find((v) => v.id === id).index*Math.round(node_size)*2"
                y1="0"
                :x2="Math.round(node_size)+variant.index*Math.round(node_size)*2"
                :y2="Math.round(node_size)*3"
                stroke="black"
            />
          </template>
        </template>
      </svg>
    </div>
    <div class="d-flex flex-nowrap">
      <div
          :style="'min-width: '+Math.round(node_size)*2+'px; height: '+Math.round(node_size)+'px; background-color: white;'"
      >
        <h3>{{genNum}}</h3>
      </div>
      <div
          v-for="variant in variants"
          :style="'min-width: '+Math.round(node_size)*2+'px; height: '+Math.round(node_size)+'px; background-color: white;'"
      >
        <div
            v-if="variant.is_copied==false"
            @click="select(variant.id)"
            class="circle center-block"
            :style="
            {
              width: Math.round(node_size)+'px',
              height: Math.round(node_size)+'px',
              background: 'rgba(0,255,0,'+parseFloat(history.variants[variant.id].fitness)+')'
            }"
            :class="
            {
              'selected-circle':variant.id===selected_variant_id,
              'parent-circle':variant.id!==selected_variant_id && parent_variant_ids.includes(variant.id)
            }"
        ></div>
        <div
            v-if="variant.is_copied==true"
            @click="select(variant.id)"
            class="mini-line center-block"
            :style="{
              width: Math.round(node_size)+1+'px', height: Math.round(node_size)*0.3+'px'
            }"></div>
        <div
            v-if="variant.is_copied==true"
            @click="select(variant.id)"
            class="mini-circle center-block"
            :style="{
              width: Math.round(node_size)/5*2+'px',
              height: Math.round(node_size)/5*2+'px',
              background: 'rgba(0,255,0,'+parseFloat(history.variants[variant.id].fitness)+')'
            }"></div>
        <template v-if="genNum !== successful_build_variant_information.length - 1">
          <div
              v-if="variant.is_copied==true && [successful_build_variant_information[genNum+1].filter((v) => v.is_copied == false).map(function(v) { return history.variants[v.id].operation.parentIds }).flat(), successful_build_variant_information[genNum+1].filter((v) => v.is_copied == true).map(function(v) { return v.id })].flat().includes(variant.id)"
              @click="select(variant.id)"
              class="mini-line center-block"
              :style="{
              width: Math.round(node_size)+1+'px',
              height: Math.round(node_size)*0.3+'px'
            }"
          ></div>
        </template>
      </div>
      <div
          v-if="failed_build_variant_information[genNum].length > 0"
          :style="'min-width: '+Math.round(node_size)*2+'px; height: '+Math.round(node_size)+'px; background-color: white;'"
      >
        <img src="@/assets/batsu.png"
             class="cross"
             :style="
             {
               width: Math.round(node_size)+'px',
               height: Math.round(node_size)+'px'
             }"
        >
        <br>
        <p>{{failed_build_variant_information[genNum].length}}</p>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: "VariantTree",
  props: ['history', 'selected_variant_id', 'updateSelectedVariantId', 'show_suspicious', 'node_size'],
  data() {
    return {
      parent_variant_ids: [],
      successful_build_variant_information: [],
      failed_build_variant_information: []
    }
  },
  created: function() {
    this.divide_variants();
  },
  methods: {
    select: function(id) {
      this.parent_variant_ids = [];
      this.$emit('updateSelectedVariantId', id);
      const stack = [];
      stack.push(id);
      var x, ids;
      while(stack.length > 0) {
        x = stack.pop();
        this.parent_variant_ids.push(x);
        ids = this.history.variants[x].operation.parentIds;
        ids.forEach(function( id ) {
          stack.push(id);
        });
      }
    },
    divide_variants: function() {
      this.successful_build_variant_information = [];
      this.failed_build_variant_information = [];
      for(let i=0;i<this.history.variants.slice(-1)[0].generationNumber+1;i++){
        this.successful_build_variant_information.push([]);
        this.failed_build_variant_information.push([]);
      }
      for(const v of this.history.variants) {
        /* ビルドの成功したVariantを世代順に振り分け*/
        if(v.isBuildSuccess == true) {
          let variant = {'id': v.id, 'is_copied': false, 'operation_name': v.operation.name}
          this.successful_build_variant_information[v.generationNumber].push(variant);
        }
        else {
          this.failed_build_variant_information[v.generationNumber].push(v);
        }
      }
      // コピーされたバリアントを追加
      for(const v of this.history.variants) {
        const selection_count = (v.id !== 0) ? v.selectionCount : v.selectionCount + 1;
        for(let i=1;i<=selection_count;i++) {
          if(typeof this.successful_build_variant_information[v.generationNumber+i].find((v2) => v2.id === v.id) === 'undefined') {
            this.successful_build_variant_information[v.generationNumber+i].push({'id': v.id, 'is_copied': true});
            console.log(v.generationNumber+i);
            console.log(v.id);
          }
        }
      }
      for(let i=this.history.variants.slice(-1)[0].generationNumber;i>0;i--){
        let variants = this.successful_build_variant_information[i].filter((v) => v.is_copied == false);
        for(const v of variants) {
          for(const id of this.history.variants[v.id].operation.parentIds) {
            if(typeof this.successful_build_variant_information[i-1].find((v) => v.id === id) === 'undefined') {
              this.successful_build_variant_information[i-1].push({'id': id, 'is_copied': true});
            }
          }
        }
        let failed_variants = this.failed_build_variant_information[i];
        for(const v of failed_variants) {
          for(const id of this.history.variants[v.id].operation.parentIds) {
            if(typeof this.successful_build_variant_information[i-1].find((v) => v.id === id) === 'undefined') {
              this.successful_build_variant_information[i-1].push({'id': id, 'is_copied': true});
            }
          }
        }
      }
      for(let i=this.history.variants.slice(-1)[0].generationNumber;i>0;i--) {
        let copied_variants = this.successful_build_variant_information[i].filter((v) => v.is_copied == true);
        for(const v of copied_variants) {
          if(typeof this.successful_build_variant_information[i-1].find((old_v) => v.id === old_v.id) === 'undefined') {
            this.successful_build_variant_information[i - 1].push({'id': v.id, 'is_copied': true});
          }
        }
      }
      // 整列しindexを付与
      for(let i=0;i<this.history.variants.slice(-1)[0].generationNumber+1;i++){
        this.successful_build_variant_information[i].sort((a, b) => a.id - b.id);
        for(let j=0;j<this.successful_build_variant_information[i].length;j++) {
          this.successful_build_variant_information[i][j]['index'] = j;
          if(!this.successful_build_variant_information[i][j].is_copied && this.successful_build_variant_information[i][j].operation_name !== 'random-crossover' && i != 0) {
            let variant = this.history.variants[this.successful_build_variant_information[i][j].id];
            let parent_variant = this.history.variants[variant.operation.parentIds[0]];
            const suspicious_values = this.calc_suspiciousness_values(parent_variant);
            let max_suspicious_value = 0.0;
            for(let k=variant.operation.appendBase.lineNumberRange.start;k<=variant.operation.appendBase.lineNumberRange.end;k++) {
              max_suspicious_value = max_suspicious_value < suspicious_values[k] ? suspicious_values[k] : max_suspicious_value;
            }
            this.successful_build_variant_information[i][j]['max_suspicious_value'] = max_suspicious_value;
          }
        }
      }
    },
    calc_suspiciousness_values: function(selected_variant) {
      var suspiciousness_values = Array(selected_variant.sourceCode.split('\n').length).fill(0.0);
      for(let suspiciousness of selected_variant.suspiciousnesses) {
        for(let i = suspiciousness.lineNumberRange.start; i <= suspiciousness.lineNumberRange.end; i++) {
          if(suspiciousness_values[i] < suspiciousness.value) {
            suspiciousness_values[i] = suspiciousness.value;
          }
        }
      }
      return suspiciousness_values;
    }
  },
  watch: {
    history: function(newVal, oldVal) {
      this.divide_variants();
    }
  }
}
</script>

<style scoped>
svg {
  overflow: visible;
}
.circle {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  margin: auto;
  border: 2px solid;
}
.mini-circle {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  /*margin: 15px auto 0;*/
  margin: auto;
  border: 2px solid;
}
.selected-circle {
  border: 6px solid;
  border-color: blue;
}
.parent-circle {
  border: 4px solid;
  border-color: #007bff;
}
.mini-line {
  width: 51px;
  height: 15px;
  margin: 0;
  border-right: 3px solid;
}
.cross {
  margin: auto;
  width: 50px;
  height: 50px;
}
.insert-line {
  stroke-dasharray: 6;
}
.replace-line {
  stroke-dasharray: 6 6 30 6 6 6;
}
.delete-line {
  stroke-dasharray: 6 6 30 6;
}
.cross-line {
  stroke-dasharray: 10 1;
}
.line {
  stroke-width: 2px;
}
.selected-line {
  stroke-width: 10px;
}
</style>
