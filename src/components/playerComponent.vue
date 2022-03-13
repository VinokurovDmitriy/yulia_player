<template>
  <div class="playerContainer">
  <video :id="id" @ended="nextSource"  onloadstart="this.volume=0">
    <source
        :src="source"
        type='video/mp4;codecs="avc1.42E01E, mp4a.40.2"'>
    Тег video не поддерживается вашим браузером.
  </video>
  </div>
</template>
<script>
export default {
  props: {
    id: {
      type: String
    },
    source: {
      type: String
    }
  },
  methods: {
    nextSource() {
        this.$emit('nextTrack')
    }
  },
  name: "PlayerComponent"
}
</script>
<style scoped>

</style>