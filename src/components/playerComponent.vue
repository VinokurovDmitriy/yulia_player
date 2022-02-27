<template>
  <video width="400" height="300" >
    <source v-for="item in sources"
            :src="'https://www.murmuriki.ru/' + item"
            type='video/mp4;codecs="avc1.42E01E, mp4a.40.2"'
            :key="item"
            :id="id"
    >
    Тег video не поддерживается вашим браузером.
  </video>
</template>

<script>
export default {
  props:{
    id:{
      type: String
    },
    sources:{
      type: Object
    }
  },
  name: "PlayerComponent"
}
</script>

<style scoped>

</style>