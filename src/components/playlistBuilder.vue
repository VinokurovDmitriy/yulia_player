<template>
  <div>
    <h4>Выберете ролики</h4>
    <div v-for="(option, index) of this.selected"
         :key="index">
      <select @change="selectVideo($event, index)" :id="index">
        <option
            v-for="(option, index) of Object.keys(this.selectOptions)"
            :key="index">{{ option }}
        </option>
      </select>
      <button @click="deleteItem($event)" :id="this.side + 'Position_' + index">-</button>
    </div>
    <select v-if="Object.keys(this.selectOptions).length > Object.keys(this.selectedOptions).length"
            @change="selectVideo($event)">
      <option
          :id="option == ''"
          :selected="false"
          v-for="(option, index) of Object.keys({'': '', ...this.selectOptions})"
          :key="index">{{ option }}
      </option>
    </select>

  </div>
</template>

<script>
export default {
  data() {
    return {
      selected: this.selectedOptions
    }
  },
  props: {
    selectOptions: {
      type: Object
    },
    selectedOptions: {
      type: Object
    },
    side: {
      type: String
    },
  },
  methods: {
    selectVideo(event, position = null) {
      console.log('selected', this.selected)
      position !== null ? this.selected[position] = event.target.value : this.selected.push(event.target.value)
      if (event.target.value != '') {
        this.$emit('setPlaylist', this.selected, this.side)
      }
    },

    deleteItem(event) {
      let position = event.target.id.split('_')[1]
      let result = this.selected.splice(position, 1)
      this.$emit('setPlaylist', result, this.side)
      console.log('selected', this.selected)
    }
  },
  name: "playlistBuilder"
}
</script>

<style scoped>

</style>