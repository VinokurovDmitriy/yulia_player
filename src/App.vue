<template>
  <div>

    <div class="playersBlock">
      <player-component :sources="Object.keys(source[leftSourcePlaylist])" id="leftPlayer"/>
<!--      <video width="400" height="300" >-->
<!--        <source src="https://disk.yandex.ru/d/-qWLklHPXAFONw/6_1.mp4" type='video/mp4;codecs="avc1.42E01E, mp4a.40.2"'>-->
<!--        <source src="https://disk.yandex.ru/d/-qWLklHPXAFONw/6_2.mp4" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'>-->
<!--        <source src="https://disk.yandex.ru/d/-qWLklHPXAFONw/6_3.mp4" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'>-->
<!--        Тег video не поддерживается вашим браузером.-->
<!--      </video>-->
<!--      <video width="400" height="300" >-->
<!--        <source src="../TEST_FOOTAGE/blue1.mp4" type='video/mp4;codecs="avc1.42E01E, mp4a.40.2"'>-->
<!--        <source src="../TEST_FOOTAGE/blue2.mp4" type='video/mp4; codecs="avc1.42E01E, mp4a.40.2"'>-->
<!--        Тег video не поддерживается вашим браузером.-->
<!--      </video>-->
    </div>
    <div class="playerTools">
      <div class="sideTools">
        LeftSide
        <select-playlist :playlists="Object.keys(source)" side="leftSource" @setSidePlaylist="setSidePlaylist"/>
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
import newScene from "./components/newScene";
import SelectPlaylist from "@/components/selectPlaylist";
import PlayerComponent from "@/components/playerComponent";

export default {
  components: {
    PlayerComponent,
    SelectPlaylist,
    newScene
  },
  data() {
    return {
      source: {
        0: {'------': {}, '6_1.mp4': {img: 'img.png'},  '6_6.mp4': {img: 'img.png'}, 'id':'empty'},
        // 0: {'------': {}, '6_1.mp4': {img: 'img.png'}, '6_2.mp4': {img: 'img_1.png'}, '6_3.mp4': {img: 'img_2.png'}},
        // 1: {'blue1.mp4': {img: 'img_2.png'}, 'blue2.mp4': {img: 'img_3.png'}},
        1: {'------': {}, '6_6.mp4': {img: 'img.png'}},
        // 2: {'green1.mp4': {img: 'img_4.png'}, 'green2.mp4': {img: 'img_5.png'}},
      },

      leftSource: {},
      rightSource: {},
      fistPlayerCount: 0,
      secondPlayerCount: 0,
      leftSourcePlaylist: 0,
      rightSourcePlaylist: 1
    }
  },
  methods: {
    setSidePlaylist(name, side){
      this[side + 'Playlist'] = name
      console.log(this[side + 'Playlist'])
    },
    addNewVideo(video, side) {
      this[side] = video
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
.playersBlock{
  display: flex;
  justify-content: center;
}
.itemRow{
  display: flex;
}
.playerTools .sideTools{
  margin: 50px;
}
.playerTools {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>