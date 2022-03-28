<template>
  <div>
    <h4>Добавьте эпизод</h4>
    <div v-for="(option, indexSelect) in  selected" :key="option">
      <a :ref="side + 'Select_' + indexSelect" class='support'
         tabindex="1">{{ this.selected[indexSelect] ? this.selected[indexSelect] : 'Выберете эпизод' }}
        <span class='tip'>
          <select-source :source="selectOptions"
                         :url="url"
                         :selectId="indexSelect"
                         @selectItem="selectItem"/>
 </span>
      </a>
      <button v-if="indexSelect === selectedOptions.length - 1 && selectOptions.length !== selected.length"
              @click="addItem($event)">+</button>
      <button v-if="indexSelect > 0" @click="deleteItem($event)" :id="this.side + 'Position_' + indexSelect">-</button>
    </div>
  </div>
</template>

<script>
import SelectSource from "@/components/selectSource";

export default {
  components: {
    SelectSource
  },
  mounted: function () {
    console.log('222222222222', this.selected, this.selectedOptions)
    this.$nextTick(function () {
      this.selected = this.selectedOptions
    })
  },
  data() {
    return {
      selected: this.selectedOptions
    }
  },
  props: {
    url: {
      type: String
    },
    selectOptions: {
      type: Array
    },
    selectedOptions: {
      type: Array
    },
    side: {
      type: String
    },
  },

  methods: {
    selectItem(selectId, name) {
       this.selected[selectId] = name;
        this.$emit('setPlaylist', this.selected, this.side)
      this.$refs[this.side +'Select_' + selectId][0].blur()
    },
    deleteItem(event) {
      let position = event.target.id.split('_')[1]
      this.selected.splice(position, 1)
      this.$emit('setPlaylist', this.selected, this.side)
    },
    addItem() {
      this.selected.push(null)
    }
  },
  name: "playlistBuilder"
}
</script>

<style scoped>
.support {
  width: 200px;
  padding: 3px;
  border: 1px solid #ccc;
  display: inline-block;
  position: relative;
  text-decoration: none;
  cursor: pointer;
}
</style>