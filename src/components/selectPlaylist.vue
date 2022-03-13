<template>
  <div class="selectPlaylistBlock">
    <h4>Выберете плейлист</h4>
    <div v-for="(item, index) in playlists" :key="item" class="selectPlaylistItem">
      <input type="radio"
             :name="side"
             :value="index"
             :checked="index == checked"
             :id="item + '_playlist'"
             @change="onChange($event)"/>
      <label :for="item + '_playlist'">{{ item }}</label>
    </div>
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
    },
    checked:{
      type: Number
    }
  },
  methods: {
    onChange(event) {
      this.$emit('setSideSelectOptions', event.target.value, this.side)
    }
  },
  name: "selectPlaylist"
}

</script>

<style scoped>
.selectPlaylistBlock{

}
</style>