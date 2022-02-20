<template>
  <div>
    <div>Player
      <video width="400" height="300" controls="controls" poster="../TEST_FOOTAGE/pict/img.png">
        <source src="../TEST_FOOTAGE/6_1.mp4" type='video/mp4;codecs="avc1.42E01E, mp4a.40.2"'>
        <source src="../TEST_FOOTAGE/6_2.mp4" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'>
        <source src="../TEST_FOOTAGE/6_3.mp4" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'>
        Тег video не поддерживается вашим браузером.
      </video>
      <img src="../TEST_FOOTAGE/pict/img.png">
    </div>
    <div class="playerTools">Settings
      <div>
        LeftSide
        <div v-for="video in Object.keys(leftSource)" :key="video" class="itemRow">
          <new-scene :source="source[0]"
                     :selected="video"
                     :selectedSource="leftSource"
                     side="leftSource"/>
          <button v-if="video in leftSource" @click="delete leftSource[video]">Удалить</button>
        </div>
        <new-scene v-if="Object.keys(source[0]).length -1 > Object.keys(leftSource).length"
                   :source="source[0]"
                   side="leftSource"
                   @addNewVideo="addNewVideo"/>
      </div>
      <div>
        rightSide
        <new-scene :source="rightSource" side="rightSource" @addNewVideo="addNewVideo"/>
        <new-scene :source="source[1]" side="rightSource" @addNewVideo="addNewVideo"/>
      </div>
    </div>
  </div>

</template>

<script>
import newScene from "@/components/newScene";

export default {
  components: {
    newScene
  },
  data() {
    return {
      source: {
        0: {'------': {}, '6_1.mp4': {img: 'img.png'}, '6_2.mp4': {img: 'img_1.png'}, '6_3.mp4': {img: 'img_2.png'}},
        1: {'blue1.mp4': {img: 'img_2.png'}, 'blue2.mp4': {img: 'img_3.png'}},
        2: {'green1.mp4': {img: 'img_4.png'}, 'green2.mp4': {img: 'img_5.png'}},
      },

      leftSource: {},
      rightSource: {},
      fistPlayerCount: 0,
      secondPlayerCount: 0,
    }
  },
  methods: {
    addNewVideo(video, side) {
      this[side] = video
      console.log('00000', this[side])
    }
  },
  name: 'App'
}
</script>

<style>
* {
  padding: 0;
  margin: 0;
}
.itemRow{
  display: flex;
}
.playerTools {
  display: flex;
  justify-content: space-around;
  align-items: center;
}
</style>