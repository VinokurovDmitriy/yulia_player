<template>
  <div v-for="(item, index) in playlists" :key="item">
    <input type="radio"
           :name="side"
           :value="item"
           :checked="side == 'leftSource' && index == 0"
           :id="item + '_playlist'"
           @change="onChange($event)"/>
    <label :for="item + '_playlist'">{{ item }}</label>
  </div>
</template>

<script>
export default {
  props: {
    playlists: {
      type: Array
    },
    side: {
      type: String
    }
  },
  methods: {
    onChange(event) {
      this.$emit('setSidePlaylist', event.target.value, this.side)
    }
  },
  name: "selectPlaylist"
}

</script>

<style scoped>

</style>