<template>
  <div v-if="Object.keys(source).length > 0">
    <select v-on:change="addSource($event)">
      <option
          v-for="key in Object.keys(source)"
          :selected="key === selected"
          :disabled="key in addedVideo"
          :key="key"
          class="icon"
          :value="key">{{ key }}
      </option>
    </select>
  </div>

</template>

<script>
export default {
  props: {
    delete:{
      type: Boolean
    },
    selectedSource:{
      type: Object
    },
    selected:{
      type: String
    },
    side:{
      type: String
    },
    source: {
      type: Object,
      required: true
    }
  },
  methods: {
    addSource(event){
      this.selectedVideo = event.target.value
      this.addedVideo[this.selectedVideo] = {selected: this.selectedVideo}
      this.selectedVideo = '-----'
      this.$emit('addNewVideo',this.addedVideo, this.side)
    },
  },
  data() {
    return {
      addedVideo: {},
      selectedVideo: '',
    }
  },
  name: "newScene"
}
</script>

<style scoped>
.icon {

}
</style>